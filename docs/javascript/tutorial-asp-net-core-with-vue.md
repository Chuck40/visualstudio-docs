---
title: "Create an ASP.NET Core app with Vue"
description: In this tutorial, you create an app using ASP.NET Core and Vue
ms.date: 02/25/2022
ms.topic: tutorial
ms.devlang: javascript
author: mikejo5000
ms.author: mikejo
manager: jmartens
ms.technology: vs-javascript
dev_langs:
  - JavaScript
ms.workload:
  - "nodejs"
monikerRange: '>= vs-2022'
---
# Tutorial: Create an ASP.NET Core app with Vue in Visual Studio

In this article, you learn how to build an ASP.NET Core project to act as an API backend and a Vue project to act as the UI.

Currently, Visual Studio includes ASP.NET Core Single Page Application (SPA) templates that support Angular, React, and Vue. The templates provide a built in Client App folder in your ASP.NET Core projects that contains the base files and folders of each framework.

Starting in Visual Studio 2022 Preview 2, you can use the method described in this article to create ASP.NET Core Single Page Applications that:

- Put the client app in a separate project, outside from the ASP.NET Core project
- Create the client project based on the framework CLI installed on your computer

> [!NOTE]
> Currently, the front-end project must be published manually (not currently supported with the Publish tool). For additional information, see [https://github.com/MicrosoftDocs/visualstudio-docs/issues/7135](https://github.com/MicrosoftDocs/visualstudio-docs/issues/7135).

## Prerequisites

Make sure to have the following installed:

- Visual Studio 2022 Preview 2 or later with the **ASP.NET and web development** workload installed. Go to the [Visual Studio downloads](https://visualstudio.microsoft.com/downloads/) page to install it for free.
  If you need to install the workload and already have Visual Studio, go to **Tools** > **Get Tools and Features...**, which opens the Visual Studio Installer. Choose the **ASP.NET and web development** workload, then choose **Modify**.
- npm ([https://www.npmjs.com/](https://www.npmjs.com/package/npm)), which is included with Node.js
- Vue CLI ([https://cli.vuejs.org/](https://cli.vuejs.org/))  

## Create the frontend app

1. In the New Project Dialog, select **Create a new project**. 

   :::image type="content" source="media/vs-2022/create-new-project.png" alt-text="Create a new project":::

1. Search for Vue in the search bar at the top and then select **Standalone JavaScript Vue Template** or **Standalone TypeScript Vue Template**.

   :::image type="content" source="media/vs-2022/vue-choose-template.png" alt-text="Choose a template":::

1. Give your project and solution a name. When you get to the **Additional information** window, be sure to check the **Add integration for Empty ASP.NET Web API Project** option. This option adds files to your Vue template so that it can be hooked up later with the ASP.NET Core project.

   :::image type="content" source="media/vs-2022/asp-net-core-vue-additional-info.png" alt-text="Additional information":::

Once the project is created, you see some new and modified files:

- aspnetcore-https.js
- vue.config.json (modified)
- HelloWorld.vue (modified)
- package.json (modified)

## Create the backend app

1. In Solution Explorer, right-click the solution name, hover over **Add**, and then select **New Project**. 

   :::image type="content" source="media/vs-2022/asp-net-core-add-project.png" alt-text="Add a new project":::

1. Search and select the ASP.NET Core Web API project.
 
   :::image type="content" source="media/vs-2022/asp-net-core-choose-web-api-template.png" alt-text="Choose the Web API template":::

1. Give your project and solution a name. When you get to the **Additional information** window, select **.NET 6.0** as your target framework.

   Once the project is created, Solution Explorer should look like this:

   :::image type="content" source="media/vs-2022/asp-net-core-with-vue-solution-explorer.png" alt-text="Take a look at Solution Explorer":::

1. Open `launchSettings.json` from the **Properties** folder, and under the profiles section for the backend app, change the default ports to 5001 and 5003.

   ```json
   "profiles": {
     "yourbackendapp": {
       "commandName": "Project",
       "launchUrl": "swagger",
       "environmentVariables": {
         "ASPNETCORE_ENVIRONMENT": "Development"
       },
       "applicationUrl": "https://localhost:5001;http://localhost:5003",
       "dotnetRunMessages": true
     },
   ```

## Set the project properties

1. In Solution Explorer, right-click the ASP.NET Core project and choose **Properties**.

   :::image type="content" source="media/vs-2022/asp-net-core-project-properties.png" alt-text="Open project properties"::: 
 
1. Go to the Debug menu and select **Open debug launch profiles UI** option. Clear the **Launch browser** option.

   :::image type="content" source="media/vs-2022/asp-net-core-with-vue-deselect-launch-browser.png" alt-text="Open debug launch profiles UI"::: 

1. Next, right-click the Vue project and select the **Properties** menu and go the **Debugging** section. Change the Debugger to launch to the **launch.json** option.
 
   :::image type="content" source="media/vs-2022/asp-net-core-with-vue-choose-debugger.png" alt-text="Choose the debugger (launch.json)":::

## Set the startup project

1. Right-click the solution and select **Set Startup Project**. Change the startup project from Single startup project to **Multiple startup projects**. Select **Start** for each project’s action.
  
1. Next, select the backend project and move it above the frontend, so that it starts up first.

   :::image type="content" source="media/vs-2022/asp-net-core-with-vue-set-first-project.png" alt-text="Choose the first startup project":::

## Start the project

1. Before you start the project, make sure that the port numbers match. Go to the *launchSettings.json* file in your ASP.NET Core project (in the *Properties* folder). Get the port number from the `applicationUrl` property.

   If there are multiple `applicationUrl` properties, look for one using an `https` endpoint. It should look similar to `https://localhost:5001`.

1. Then, go to the *vue.config.js* file for your Vue project. Update the target property to match the `applicationUrl` property in  *launchSettings.json*. When you update it, that value should look similar to this:

   ```js
   target: 'https://localhost:5001',
   ```

1. To start the project, press **F5** or select the **Start** button at the top of the window. You will see two command prompts appear:

   - The ASP.NET Core API project running
   - The Vue CLI running the vue-cli-service serve command

   >[!NOTE]
   > Check console output for messages, such as a message instructing you to update your version of Node.js.

You should see the Vue app appear, that is populated via the API.

## Troubleshooting

### Proxy error

You may see the following error:

```
[HPM] Error occurred while trying to proxy request /weatherforecast from localhost:4200 to https://localhost:5001 (ECONNREFUSED) (https://nodejs.org/api/errors.html#errors_common_system_errors)
```

If you see this issue, most likely the frontend started before the backend. Once you see the backend command prompt up and running, just refresh the Vue app in the browser.

Otherwise, if the port is in use, try 5002 in *launchSettings.json* and *vue.config.js*.

### Docker

If you enable Docker support while creating the web API project, the backend may start up using the Docker profile and not listen on the configured port 5001. To resolve:

Edit the Docker profile in the launchSettings.json by adding the following properties:

```json
"httpPort": 5003, 
"sslPort": 5001  
```

Alternatively, reset using the following method:

1. In the Solution properties, set your backend app as the startup project.
1. In the Debug menu, switch the profile using the **Start** button drop-down menu to the profile for your backend app.
1. Next, in the Solution properties, reset to multiple startup projects.
