# model.derivative-csharp-context.menu

![Platforms](https://img.shields.io/badge/platform-Windows-lightgray.svg)
![.NET](https://img.shields.io/badge/.NET-4.5-blue.svg)
[![ASP.NET](https://img.shields.io/badge/ASP.NET-4.5-blue.svg)](https://asp.net/)
[![License](http://img.shields.io/:license-mit-blue.svg)](http://opensource.org/licenses/MIT)

# Description

This sample will add a Windows Explorer context menu "Extract Properties" for Revit files. The desktop app will upload the .RVT to the Forge Model Derivative API, translate and extract all properties into a Excel file. It was written in C# for Windows (tested on Windows 10) and it includes 3 projects: 

**1. CSShellExtContextMenuHandler**: Class Library (.DLL) that implement the required COM interface to extend the Windows Explorer context menu. The original source code is available at this [Code Project article](https://www.codeproject.com/articles/174369/how-to-write-windows-shell-extension-with-net-lang).

**2. Translator**: WinForm .EXE that contains a basic interface and handles upload of files, download of results and notifications.

**3. TranslatorServer**: ASP.NET project that handles all Forge related tasks, hiding those operations from the end-user. Forge Client ID & Secret are used here.

## Demonstration

See [this video demonstration](https://www.youtube.com/watch?v=RNMJKjLdLS4).

The new option should appear on the Windows Explorer right-click context menu:

![](menu.png)

A notification ballon indicate the overall process:

![](notifications.png)

When ready, the Excel file will be downloaded to the same folder as the original Revit file.

# Security

Your Forge Client ID & Secret **should never** be exposed or embedded on a desktop application, it is never safe. Your write-enabled token should also not be send to a desktop application. There are several articles about that available on the web.

This sample keeps all Forge related information on the **TranslationServer** and only send a random GUID to the desktop application (**Transaltor.exe**) that allow access to the resulting Excel and expires after 24 hours (same as [Transient Bucket retention policy](https://developer.autodesk.com/en/docs/data/v2/overview/retention-policy/)). The desktop app keeps this GUID in memory and use it to request the status (progress) and download the Excel file when it's done.

# Setup

Install [Visual Studio 2015](https://www.visualstudio.com/).

Clone this project or download it. It's recommended to install [GitHub desktop](https://desktop.github.com/). To clone it via command line, use the following (**Git Shell** on Windows):

    git clone https://github.com/autodesk-forge/model.derivative-csharp-context.menu

For using this sample, you need an Autodesk developer credentials. Visit the [Forge Developer Portal](https://developer.autodesk.com), sign up for an account, then [create an app](https://developer.autodesk.com/myapps/create) that uses Data Management and Model Derivative APIs. For this new app, use **http://localhost:3000/api/forge/callback/oauth** as Callback URL, although is not used on 2-legged flow. Finally take note of the **Client ID** and **Client Secret**.

At the **TranslatorServer** project, open the **web.config** file and adjust the appSettings (for deployment, use host settings instead):

```xml
<appSettings>
   <add key="FORGE_CLIENT_ID" value="<<Your Client ID from Developer Portal>>" />
   <add key="FORGE_CLIENT_SECRET" value="<<Your Client Secret>>" />
</appSettings>
```

Compile the solution, Visual Studio should download the NUGET packages ([Autodesk Forge](https://www.nuget.org/packages/Autodesk.Forge/), [RestSharp](https://www.nuget.org/packages/RestSharp) and [Newtonsoft.Json](https://www.nuget.org/packages/newtonsoft.json/))

The CSShellExtContextMenuHandler.dll must be registered with Admin level permissions on the local machine:

    regasm.exe CSShellExtContextMenuHandler.dll /codebase

Run should start the **TranslatorServer** web app.

Right-click on a .RVT file and select the "Extract Properties" menu option, it should trigger the "Translator.exe" on the same folder as the DLL. 

# Deployment

The **TranslatorServer** should be deployed to a ASP.NET compatible host, like Azure or Appharbor. For Appharbor deployment, following [this steps to configure your Forge Client ID & Secret](http://adndevblog.typepad.com/cloud_and_mobile/2017/01/deploying-forge-aspnet-samples-to-appharbor.html).

Adjust the **Translator** desktop app app.config with the server address:

```xml
<appSettings>
   <add key="TranslatorServer" value="https://YOUR_DOMAIN_NAME.COM"/>
</appSettings>
```

# License

This sample is licensed under the terms of the [MIT License](http://opensource.org/licenses/MIT).
Please see the [LICENSE](LICENSE) file for full details.

## Written by

Augusto Goncalves [@augustomaia](https://twitter.com/augustomaia), [Forge Partner Development](http://forge.autodesk.com)
