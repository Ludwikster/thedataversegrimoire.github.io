---
layout: post
title: "The First Summoning"
date: 2025-10-04
tags:
  - PowerPlatform
  - Dataverse
  - D365
  - Plugin
---

After downloading **PAC CLI**, open Windows Terminal, navigate to the proper destination, and run the following command:

```powershell
pac plugin init -o {NAME-OF-YOUR-SLN}
```
Result:
![First Plugin](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/FirstPlugin.png)

This command will create a solution that contains the chosen name. The advantage of this approach is that your plugins will be created as NuGet packages, which can include external DLLs such as Newtonsoft.Json.

```csharp
Plugin1.cs
```
Above you can see your plugin class, where you will provide the necessary changes and logic. I'll rename Plugin1.cs to FirstPlugin.cs.

I prefer to separate methods into repositories or so-called domains—one place to rule them all (in terms of functionality 😉). Let's add a Dataverse repository that retrieves all Contacts from our system. Create a new class called DataverseRepository.

![DataverseRepository](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/DataVerseRepository.png)

```csharp
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xrm.Sdk;
using Microsoft.Xrm.Sdk.Query;

public class DataverseRepository
{
    private readonly IOrganizationService _orgService;

    public DataverseRepository(IOrganizationService orgService)
    {
        this._orgService = orgService;
    }

    public List<Entity> GetAllContacts()
    {
        var query = new QueryExpression();
        query.EntityName = "contact";
        query.ColumnSet = new ColumnSet(true); // PLEASE DON'T ;) Always query only what you require. Nothing more, nothing less.
        return this._orgService.RetrieveMultiple(query).Entities.ToList();
    }
}
```

After that you are able to use the Dataverse repository in your plugin:

```csharp
using System;

namespace FirstPlugin
{
    public class FirstPlugin : PluginBase
    {
        public FirstPlugin(string unsecureConfiguration, string secureConfiguration)
            : base(typeof(FirstPlugin))
        {
        }

        protected override void ExecuteDataversePlugin(ILocalPluginContext localPluginContext)
        {
            if (localPluginContext == null)
            {
                throw new ArgumentNullException(nameof(localPluginContext));
            }

            var context = localPluginContext.PluginExecutionContext;
            // Elevated organization service allows us to use the plugin in the context of System. Powerful tool—treat it with care.
            var elevatedOrganizationService = localPluginContext.OrgSvcFactory.CreateOrganizationService(null);
            var dataverseService = new DataverseRepository(elevatedOrganizationService);
            var contacts = dataverseService.GetAllContacts();
            // After retrieving contacts, you can extend logic as you please—or better yet, as requirements dictate.
        }
    }
}
```