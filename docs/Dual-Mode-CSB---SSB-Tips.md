## Breaking changes in v. 0.6.0 for Dual Mode

Solution comes with CSB as default. To switch is fairly easy, just change your Solution Configuration to **Debug_SSB** in the Debug Toolbar or from the menu Build -> Configuration Manager -> Change **Debug_CSB** to **Debug_SSB**

 - One thing to note is that if you do this from CSB to SSB you might get an error if the error is something like below then you will want to open the url in another browser or in Incognito mode. The error is the browser caching the app and needs to be cleared:

`{"Version":"0.6.0","StatusCode":500,"IsError":false,"Message":"Request responded with exceptions.","ResponseException":{"IsError":true,"ExceptionMessage":"Object reference not set to an instance of an object.","Details":"   at BlazorBoilerplate.CommonUI.Shared.Components.UserProfile.`

- The Solution has been updated with the Pages in a Common project to allow for Dual Mode.

- Index.html / _hosts.cshtml 
  - CSB _index.html_ is now **BlazorBoilerplate.CommonUI/wwwroot/index_csb.html**
  - SSB __hosts.cshtml_ is now **BlazorBoilerplate.Server/Pages/index_ssb.cshtml**
