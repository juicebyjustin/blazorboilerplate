# Entity Framework
Entity Framework (EF) Core is a lightweight, extensible, open source and cross-platform version of the popular Entity Framework data access technology.

Documentation can be found [here](https://docs.microsoft.com/en-us/ef/core/).

## Setup
1. Install/Update Entity Framework Core CLI
    * Install: `dotnet tool install --global dotnet-ef`
    * Update: `dotnet tool update --global dotnet-ef`

Source: https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dotnet

## DB Contexts
1. ApplicationDbContext - main db context where all your objects are placed.
2. PersistedGrantDbContext - used for [Identity Server 4](https://github.com/IdentityServer/IdentityServer4/releases).
3. ConfigurationDbContext - used for [Identity Server 4](https://github.com/IdentityServer/IdentityServer4/releases).

### Identity Server 4
There are 2 contexts required for Identity Server 4 (IS4). If new versions of IS4 change the db contexts migrations must be added.

[Using EntityFramework Core for configuration and operational data](http://docs.identityserver.io/en/release/quickstarts/8_entity_framework.html#using-entityframework-core-for-configuration-and-operational-data)

#### How to Run Migration for IS4 Update
**Will be expanded in the future.**

[Database Schema Changes and Using EF Migrations](http://docs.identityserver.io/en/release/quickstarts/8_entity_framework.html#database-schema-changes-and-using-ef-migrations)

## Migrations
**Keep in mind that every DbContext has its own migrations.**

The migrations in BlazorBoilerplate are stored in the `BlazorBoilerplate.Storage` project. You have to open your shell in this path. Then you have to specify the project where the db contexts are added to the services, because **dotnet ef** has to know how to find and instantiate them. In our case the so called startup project is `BlazorBoilerplate.Server`. 

The commands are:

ApplicationDbContext: `dotnet ef --startup-project ../BlazorBoilerplate.Server/ migrations add 4Preview3 -c ApplicationDbContext --verbose --no-build --configuration Debug_CSB`

PersistedGrantDbContext: `dotnet ef --startup-project ../BlazorBoilerplate.Server/ migrations add 4Preview3 -c PersistedGrantDbContext --verbose --no-build --configuration Debug_CSB`

ConfigurationDbContext: `dotnet ef --startup-project ../BlazorBoilerplate.Server/ migrations add 4Preview3 -c ConfigurationDbContext --verbose --no-build --configuration Debug_CSB`

Without the `--no-build` parameter dotnet rebuilds the startup project, but if you have just built it with Visual Studio it is just a waste of time. Also the dotnet build is not the Visual Studio build, so to avoid issue use `--no-build`.

The name of migration, in this case _4Preview3_, is only descriptive and it does not need to be unique, because automatically the name is prepended with date time.

If the command is successful, `[datetime]_[migration-name].cs` is added to the solution. Also the `[db-context-name]ModelSnapshot.cs` is updated to reflect the db changes in case a new db has to created.

### Create a Migration
1. Open the package manager console.
1. cd to Server/BlazorBoilerplate.Storage.
1. Set the startup project to BlazorBoilerplace.Server
1. Run the command listed above pending on which context you are using and give a custom migration name.
1. Find the migration file in `BlazorBoilerplate.Storage.Migrations`.

### Updating the Database

When you run the project, the migrations are applied to the database. The db table `__EFMigrationsHistory` keeps track of applied migrations without information about the related db context. 

To get this information use the following command; for example for ConfigurationDbContext:
```
dotnet ef --startup-project ../BlazorBoilerplate.Server/ migrations list -c ConfigurationDbContext --verbose --no-build --configuration Debug_SSB
```

You can also update the database without running the project with the following command (the migration name `4Preview3` is not required):

```
dotnet ef --startup-project ../BlazorBoilerplate.Server/ database update 4Preview3 -c ConfigurationDbContext --verbose --no-build --configuration Debug_SSB
```

**If you specify a previous migration, you can revert the db changes to that migration.**

## Warning
The migrations are not smart, when you have an existing db with populated tables. If migrations add keys and/or unique indexes or something else violating referential integrity, you have to manually modify `[datetime]_[migration-name].cs` for example to add some SQL to fix the errors. 

E.g. `migrationBuilder.Sql("UPDATE AspNetUserLogins SET Id=NEWID() WHERE Id=''");` to add unique values to a new field before setting as a new primary key.

### Troubleshooting
* No migration is created or the migration is blank. 
    * Make sure you build the solution.
* The database update did not update the database.
    * Make sure you build the solution.
    * Make sure the db connection string is correct.