---
layout: post 
title: "🕯️First Script Magic🕯️" 
date: 2026-01-03 
tags: 
  - PowerPlatform
  - Dataverse
  - D365
  - FrontEnd
---

Using JavaScript in CRM was allowed since the old age of on‑premise environments. By reading the text below you will be able to learn how to begin your journey with JS in PowerPlatform. Create yourself a folder and JavaScript file to store and structure your code:

![FirstScriptPath](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/FirstScriptPath.png)

Create a web resource in the environment:

![CreateWebResource](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/CreateWebResource.png)

Fill out the necessary information and press save. Then you can go back to your JS file and start with simple code such as:

```js
var AccountMainLib = AccountMainLib || {}; 
(function (AccountMainLibNS) { 
    AccountMainLibNS.checkPhoneNumber = function (executionContext) { 
        var formContext = executionContext.getFormContext(); 
        var phoneNumberAttribute = formContext.getControl('telephone1').getAttribute(); 
        if (!phoneNumberAttribute) return; 

        var phoneNumberValue = phoneNumberAttribute.getValue(); 
        if (!phoneNumberValue) return; 

        var hasCountryCode = AccountMainLibNS.hasCountryCode(phoneNumberValue); 

        if (!hasCountryCode) { 
            formContext.ui.setFormNotification('This Account does not have country code!🧙', 'WARNING', 1); 
            } 
    }; 

    AccountMainLibNS.hasCountryCode = function (number) { 
        return /^\+?[1-9]\d{1,3}/.test(number); 
    };
})(AccountMainLib);
```

As You can see I'm using:

```js
executionContext
```

and

```js
formContext
```

Those two are most used and most important variables in scripting as those are main source of information.

```js
Xrm
```

This is library provisoned by D365. More information what it can do You will be able to find in link below.

**Client API Reference**

https://learn.microsoft.com/en-us/power-apps/developer/model-driven-apps/clientapi/reference

This is documentation including every and each function that You are able to use within modelderiven app and...in more places 🧙‍♂️

After checking what You can do with above link please copy paste Your code into a library that You've created at the begining.

Now We will learn how to add simple script on Load of our form:
Go back to PowerPlatform and within your table you will be able to find Form tab:

![FormEdit](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/FormEdit.png)

Choose form to which You would like to add your script and click edit.
You will be prompted with edit mode of a form.
Select JS tile in a menu:

![JSLibs](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/JSLibs.png)

Then "+ Add library", find You library in search bar,select it, hit "Add" button and You will be able to reference Your function in Your form.

To the right on Your page You will be able to see possibility to add events :

![AddEventOnLoad](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/AddEventOnLoad.png)

Click "+ Event Handler" in "On Load" section. After filling out necessary information You've successfully attached Your first script!

![EventConfiguteOnLoad](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/AddEventOnLoad.png)

Check if it works by going into You form at any record!

![FirstJSWorks](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/FirstJSWorks.png)
