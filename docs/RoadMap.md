# Blazor Boilerplate Project Roadmap

Blazor Boilerplate has a decent start but we have several features that could use additional collaborator support to tackle. Below is the initial list. As more collaborator's join please make suggestions on the [Gitter community](https://gitter.im/blazorboilerplate/community)

### Project | Assigned Contributor
* Review Logging and Test on local & published | n/a
* Add Local Storage for User Profile & Theme options | n/a
* Create Wiki for Deployment both IIS and Azure | n/a
* Simple CRUD for Customers -> Orders or or Blog | n/a
* Unit, Integration and Automatized Tests | n/a
* Simple Chat using SignalR - maybe this repo can be a resource: https://github.com/conficient/BlazorChatSample | n/a

### Development branch
#### Breeze
[Breeze](http://breeze.github.io/) integration completed with ToDo example.

With Breeze the API design takes less code and makes easy some complex update.

In this project the [Breeze Controller](http://breeze.github.io/doc-net/webapi-controller-core.html) is [ApplicationController](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Server/BlazorBoilerplate.Server/Controllers/ApplicationController.cs). The [EFPersistenceManager](http://breeze.github.io/doc-net/ef-efpersistencemanager-core.html) is [ApplicationPersistenceManager](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Server/BlazorBoilerplate.Storage/ApplicationPersistenceManager.cs) and the [Breeze.Sharp Entities](http://breeze.github.io/doc-cs/entities-and-complexobjects.html) (i.e. the Dto) are in [BlazorBoilerplate.Shared.Dto.Db](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/BlazorBoilerplate.Shared/Dto/Db).

The [breeze.sharp library](https://github.com/Breeze/breeze.sharp) is rarely updated, so to make BlazorBoilerplate working with Breeze I used my [fork](https://github.com/GioviQ/breeze.sharp). You can find the package in folder [nupkg](https://github.com/enkodellc/blazorboilerplate/tree/development/nupkg) and [nuget.config](https://github.com/enkodellc/blazorboilerplate/blob/development/src/nuget.config) tells Visual Studio to use this folder as a package source.

The [ApplicationController](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Server/BlazorBoilerplate.Server/Controllers/ApplicationController.cs) has no Authorize attribute, because to keep policy management with Breeze, I implemented a simple custom policy management in [ApplicationPersistenceManager](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Server/BlazorBoilerplate.Storage/ApplicationPersistenceManager.cs).

First of all you can mark the [EF entities](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/BlazorBoilerplate.Infrastructure/Storage/DataModels) with [Permissions attribute](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/BlazorBoilerplate.Infrastructure/AuthorizationDefinitions/PermissionsAttribute.cs) which takes [CRUD Actions flag enum](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/BlazorBoilerplate.Infrastructure/AuthorizationDefinitions/Actions.cs) as parameter.
Examples:


    [Permissions(Actions.Delete)]
    public partial class Todo
    {
     ...
    }

    [Permissions(Actions.Create | Actions.Update)]
    public partial class Todo
    {
     ...
    }

    [Permissions(Actions.CRUD)]
    public partial class Todo
    {
     ...
    }

In BeforeSaveEntities method of [ApplicationPersistenceManager](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Server/BlazorBoilerplate.Storage/ApplicationPersistenceManager.cs) the actions of the entities to be saved are compared with the claims of permission type of the current user. If a policy is violated an EntityErrorsException is thrown.

To check Actions.Read in ApplicationController you have to access EF DbSet with GetEntities method of ApplicationPersistenceManager.

Auditable shadow properties are nice, but what about reading in ToDo list page who created a Todo and when? Shadow properties are not exposed, so to expose them without manually create them for every entity implementing IAuditable, I introduced [Source Generator](https://devblogs.microsoft.com/dotnet/introducing-c-source-generators/) with project [BlazorBoilerplate.SourceGenerator](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Utils/BlazorBoilerplate.SourceGenerator).

[AuditableGenerator](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Utils/BlazorBoilerplate.SourceGenerator/AuditableGenerator.cs) generates for every class implementing IAuditable the needed properties. Remember to make partial all classes implementing IAuditable.

To access ApplicationController from client, you have to inject IApiclient ([ApiClient](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/BlazorBoilerplate.Shared/Services/ApiClient.cs)).
#### Modularization
Goal: web application starts and reads from database which modules to load for the current tenant.

At the moment the modules are implemented in folder [Modules](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/Modules):

[BlazorBoilerplate.Theme.Material](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/Modules/BlazorBoilerplate.Theme.Material) is the main theme with general purpose pages. It is based on [MatBlazor](https://www.matblazor.com/). All UI modules depends on main theme.

[BlazorBoilerplate.Theme.Material.Admin](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/Modules/BlazorBoilerplate.Theme.Material.Admin) is the admin UI. If the current tenant is the master tenant, you can manage tenant CRUD actions.

[BlazorBoilerplate.Theme.Material.Demo](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/Modules/BlazorBoilerplate.Theme.Material.Demo) contains the application UI, in this case BlazorBoilerplate Demo.

[BlazorBoilerplate.GoogleAnalytics](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/Modules/BlazorBoilerplate.GoogleAnalytics) injects Google Analytics script with TrackingId read from appsettings.json.

Every module has to contain a class implementing [IModule](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/BlazorBoilerplate.Shared/Interfaces/IModule.cs) or deriving from [BaseModule](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/BlazorBoilerplate.Shared/Models/BaseModule.cs). If the module is also a theme a class has to implement [ITheme](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/BlazorBoilerplate.Shared/Interfaces/ITheme.cs). The project file .csproj contains

    <Target Name="PostBuild" AfterTargets="PostBuildEvent">
      <Exec Command="copy $(TargetPath) $(SolutionDir)Server\BlazorBoilerplate.Server\Modules\" />
    </Target>

to copy dll in Modules folder. Sometimes Visual Studio does not compile automatically the project if you run Debug without compile solution. 

If the module has wwwroot folder this implementation does not take in consideration, so for now all modules have been statically referenced in solution. Future goal: package modules as nupkg and implement something to load them like in the Oqtane framework.

A module can add [ITagHelperComponent](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.razor.taghelpers.itaghelpercomponent) to inject something in [_Index.html](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Server/BlazorBoilerplate.Server/Pages/_Index.cshtml) like e.g. [GoogleAnalyticsTagHelperComponent.cs](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/Modules/BlazorBoilerplate.GoogleAnalytics/GoogleAnalyticsTagHelperComponent.cs).

A module can add pages and inject [IDynamicComponent](https://github.com/enkodellc/blazorboilerplate/blob/development/src/Shared/BlazorBoilerplate.Shared/Interfaces/IDynamicComponent.cs) in NavMenu, Footer, DrawerFooter and TopRightBarSection of main theme module like [BlazorBoilerplate.Theme.Material.Demo](https://github.com/enkodellc/blazorboilerplate/tree/development/src/Shared/Modules/BlazorBoilerplate.Theme.Material.Demo).

#### Multitenancy
Goal: add multitenancy based on hostname to make [blazor-server.quarella.net](https://blazor-server.quarella.net/) and [blazor-wasm.quarella.net](https://blazor-wasm.quarella.net/) two tenants.

Read: [Multitenancy](https://github.com/enkodellc/blazorboilerplate/wiki/Multitenancy).