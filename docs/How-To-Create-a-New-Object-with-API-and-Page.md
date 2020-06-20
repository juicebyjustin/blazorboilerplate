# How to Create a New Object

Creating a new object that is tied to the database and API is easy. You can use `Todo` as a base for your new object. 

To get a feel of what all needs to be created for your new object you should view all references of `BlazorBoilerplate.Shared.Dto.Db.Todo.cs` and `BlazorBoilerplate.Infrastructure.Storage.DataModels.Todo.cs`.

1. Add your new object to:
    * BlazorBoilerplate.Infrastructure.Storage.DataModels
    * BlazorBoilerplate.Shared.Dto.Db
1. Add DbSet for new object to:
    * ApplicationDbContext.cs
    * IApplicationDbContext.cs
1. Build the project.
1. Add and complete a new Entity Framework migration per the Entity Framework wiki page.

## Add API Calls
All new API calls are placed in the class `BlazorBoilerplate.Shared.Services.ApiClient.cs`.

* Add new get, put, post, etc methods to:
    * IApiClient.cs
    * ApiClient.cs

Upon building and running the solution you will see the new API calls in Swagger.

## Add a Page for New Object
The following will render you with a working CRUD page for your new object.

1. Add a new `<MatNavItem>` to the file `BlazorBoilerplate.Theme.Material.Demo.Shared.Components.NavMenu.razor`.
1. Make a copy of `Todo.razor`.
1. Run a find and replace maching case. You will likely have to rename all method calls for the API that find and replace did not catch.
    * Find `Todo`
    * Replace `Your New Object Name`

You now have a navigation link to your new page and a new page with CRUD for your new object.