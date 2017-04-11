## Sending results back to your browser

Why not use SignalR? 

### Architecture

What are we building? 
1. The profile page establishes a signalr listener on load
2. When AZure function finishes, it posts to a Web API endpoint hosted in our website
3. The Web API enpoint pushes a SignalR message to it's lisnters, using a server broadcast
4. listener on the page determines if it's image is the once that was processed, then reloads the img source by appending a querystring to the image source URL

### Install the SignalR NuGet packages

- Microsoft.AspNet.SignalR
- Microsoft.AspNet.SignalR.Core
- Microsoft.AspNet.SignalR.JS
- Microsoft.AspNet.SignalR.SystemWeb

### establish listener on Index.cshtml page

- add script to Index.cshtml page

### Create a singalR hub

definition

- Add class ProfilePicHub.cs to the root of the project
- Then need to create a class that is responsible for broadcasting a message. Create ProfilePicture

### Configure SignalR to start with the app

- Startup.cs - add line app.MapSignalR(); 

### Install WebAPI Nuget packages

- Microsoft.AspNet.WebApi
- Microsoft.AspNet.WebApi.Core
- Microsoft.AspNet.WebApi.WebHost
- Microsoft.AspNet.WebApi.Client

### Configure Web Api in solution

- create WebApiConfig.cs file in App_Start folder
- update Global.asax.cs file - using System.Web.Http;
- update Global.asax.cs file - add GlobalConfiguration.Configure(WebApiConfig.Register) before the route

### Add a ProfilePictureController class

- profilepicture controller class, add code

### update Azure Function

- add more signal r code

### test it

- deploy your website to azure and watch the magic happen