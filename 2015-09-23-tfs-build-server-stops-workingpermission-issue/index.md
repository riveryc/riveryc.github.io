# TFS Build Server Stops Working - Permission Issue


### We got an issue for TFS build server today, it doesn’t build anything.

#### Symptoms

* From Visual Studio, we cannot see any progress for the build.
* From the build server, we cannot see any build being triggered, all agents status are “Ready”.

<!--more-->

#### Investigation:

* Check the connectvity between Client to TFS Web Service — OK
* Check the connectivity between TFS Web Server to TFS Build Server — OK
* Check the connectivity between Client to TFS Build Server — OK
* Restart the TFS Build Controller — Still not working
* Restart the TFS Build Server — Still not working
* Check the event log on TFS Build Server
* Applications and Services Logs -> Microsoft -> Team Foundation Server -> Build-Services -> Operational
* There are few “Critical” Error message:

```Dos
Default Controller — [BuildServerName]: Failed to update build vstfs:///Build/Build/[BuildNumber] on server https://[tfs Collection] due to invalid permission configuration.
```

* Follow by the “Critical” Error, we have another “Error”:

```Dos
Default Controller — [BuildServerName]: Failed to finalize build vstfs:///Build/Build/[BuildNumber] due to an exception.
Exception Type: Microsoft.TeamFoundation.Build.Client.AccessDeniedException
Exception Message: TF215106: Access denied. [An AD Account] needs Update build information permissions for build definition [Build Defination Name] in team project [Project Name] to perform the action. For more information, contact the Team Foundation Server administrator.
Stack Trace: at Microsoft.TeamFoundation.Client.Channels.TfsHttpClientBase.HandleReply(TfsClientOperation operation, TfsMessage message, Object[]& outputs)
at Microsoft.TeamFoundation.Build.Client.BuildWebService4.UpdateBuildInformation(InformationChangeRequest[] changes)
at Microsoft.TeamFoundation.Build.Client.InformationNodeConverters.BulkUpdateInformationNodes(BuildDetail build, List`1 requests)
at Microsoft.TeamFoundation.Build.Client.BuildInformation.Save()
at Microsoft.TeamFoundation.Build.Hosting.BuildControllerWorkflowManager.FlushBuild(IBuildDetail build, BuildStatus finalStatus)
at Microsoft.TeamFoundation.Build.Hosting.BuildControllerWorkflowManager.OnInstanceCompleted(BuildWorkflowInstance instance, IDictionary`2 outputs, Exception terminationException)
```

#### From my understanding, the workflow for triggering a build should be:

1. User Trigger a build in Visual Studio
2. Visual Studio requests the job by using TFS Web Service
3. TFS Web Service will create entry in TFS DB, and communicate the TFS Build Server to build. (Not sure if this is correct or not)
4. TFS Build Server will need to talk back to TFS Web Service Server to update TFS DB for the Build information
5. TFS Build Server will need to give the job to one of the build agents and build the solution…

#### Based on the error message we found there, I think the root cause is on 4, because the AD Account doesn’t have permission to to update the build information from Build Server

#### But we never had this problem before by using this service account, and it should have all admin permission for TFS.

#### By chekcing the permission setup in TFS, I’ve confirmed this account doesn’t have any problem with security settings.

#### Secondly, I tried use this service account to login to TFS Web Service, it didn’t work saying permission issue.

#### Finally, the wrong credential was found in Credential manager, There is a record hard coded for TFS Web Service address with the AD account mentioned above.

#### After removing the record, everything comes back online. :)
