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

After downloading **PAC CLI** open windows terminal go to proper destination and write down below comand: 

```powershell
pac plugin init -o {NAME-OF-YOUR-SLN}
```
Result :

![First Plugin](/images/FirstPlugin.png)

This comand will create a an solution that will contain choosen name.
Adventage of that aprouch is : Your plugins will be created as a nuget packegs which will contain external dlls like Newtonsof-Json.

Beginings: 
```csharp 
Plugin1.cs 
``` 
Above Tou will be ableto see Your plugin class in which You will provide necessary changes and logick.

I prefere to seperate methods into repositories or as so called domains once place to rule them all ( in the matter of functionality ;) ).
Lets add Dataverse repository which will get all Contacts from our system: 
Create a new class called DataverseRepository.

![Dataverse Repository](/images/DataVerseRepository.png)

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
        query.ColumnSet = new ColumnSet(true); //PLEASE DON'T ;) Always Query what You requeire. Nothing more nothing less... 
        return this._orgService.RetrieveMultiple(query).Entities.ToList();
    }
}
```

After that You are able to use Dataverse repository in Your plugin : 
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
            // Elevated organisation service allows us to use plugin in context of System. Powerfull tool. Treat it with care.
            var elevatedOrganisationService = localPluginContext.OrgSvcFactory.CreateOrganizationService(null); 
            var dataverseService = new DataverseRepository(elevatedOrganisationService);
            var contacts = dataverseService.GetAllContacts();
            //After retrieving contacts You can extend logick as You please or better...as requierments says.
        }
    }
}
```
