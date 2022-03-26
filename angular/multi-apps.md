### Multiple projects
A multi-project workspace is suitable for an enterprise that uses a single repository and global configuration 
for all Angular projects (the "monorepo" model). A multi-project workspace also supports library development.

### Advantages
- A single source-control repository (E.g. git).
- Time-consuming — no need to run `npm install` for every app.
- Saving disk space by having a shared `node_modules` folder for all apps.
- Easy to update to the next version for all apps.

### Setup
If you intend to have multiple projects in a workspace, you can skip the initial application generation when you 
create the workspace, and give the workspace a unique name. The following command creates a workspace with all of the workspace-wide configuration files, but no root-level application.
```
ng new multi-app --create-application false
```
The above command creates folder with the name `multi-app` and configures the workspace. It does not create any apps.
You can then generate applications and libraries with names that are unique within the workspace.

```
cd multi-app
```

### Add Another Project to Workspace

```
ng generate application dashboard
ng generate application admin
```

### angular.json
The `angular.json` contains the configuration settings for the workspace. Here is the shortened version of angular.json for the above code.
```
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",     <== location of the apps
  "projects": {
    "dashboard": {
         //This section contains the setting for the gettingStarted project      
    },
    "admin": {
        //This section contains the setting for the exampleApp project
    }},
  "defaultProject": "dashboard"    <== name of the default project
}
```

### Run the App
There are 3 ways to run the app.
- `ng serve dashboard`
- Use the project flag `ng serve --project="dashboard"`
- Open the `angular.json` and locate the `defaultProject` at the bottom. Set it to `dashboard` and run `ng serve`

### Build App for Production
To build the app for production, we can use `ng build` command with project flag
```
ng build --prod --project="dashboard"
ng build --prod --project="admin"
```
