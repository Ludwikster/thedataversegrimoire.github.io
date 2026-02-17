---
layout: post 
title: "🕯️Type Script in Dataverse🕯️" 
date: 2026-02-16 
tags: 
  - PowerPlatform
  - Dataverse
  - D365
  - FrontEnd
---

Probably you know this but We powerplatform wizards are able to use TypeScript to create our FrontEnd functionalities.

To make it happen create folder in directory that you would like to store your project.
Open visual studio in Your newly created location and type in below coment into terminal

```powershell
npm install --save-dev typescript @types/xrm
```

Inside your folder create file tsconfig.json

```json
{
    "compilerOptions": {
        "target": "ES5",
        "module": "none",
        "outDir": "dist",
        "rootDir": "src",
        "strict": true,
        "types": [
            "xrm"
        ],
        "lib": [
            "DOM",
            "ES5"
        ]
    },
    "include": [
        "src/**/*"
    ]
}
```

After that create a folder structure :

TS/
│
├── src/
│   └── account/
│       └── account-main.ts
│
├── dist/
│   └── (compiled JS goes here)
│
├── tsconfig.json
└── package.json

Adjust package.json to look like so :

```json
{
  "devDependencies": {
    "@types/xrm": "^9.0.88",
    "ts-node": "^10.9.2",
    "typescript": "^5.9.3"
  },
  "scripts": {
    "build": "tsc",
    "watch": "tsc --watch"
  }
}
```

As You can see I've created account-main.ts which will be my library:

```js
class AccountMainLib {

    static checkPhoneNumber = (executionContext: Xrm.Events.EventContext) =>{
        const formContext = executionContext.getFormContext();
        const phone = formContext.getAttribute("telephone1")?.getValue();

        if (!phone) return;

        if (!AccountMainLib.hasCountryCode(phone as string)) {
            formContext.ui.setFormNotification(
                "This Account does not have country code! 🧙",
                "WARNING",
                "phone-warning"
            );
        }
    }

    static hasCountryCode = (number: string): boolean => {
        return /^\+?[1-9]\d{1,3}/.test(number);
    }
}
```

Run :

```powershell
npm run build
```

Your transpiled JS will be visible in disti folder!

![TSStructureNoDataverseIfy](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/TSStructureNoDataverseIfy.png)

You can just copy paste it to your webresource and We are all done!
***Dataverse-ify***
But...TypeScript as name suggests is strongly typed whom would have thought?😉
Below You can follow up link to package created by Scott Durow that will provide you attributes, table metadata and many many more! (🎆Pure awesomnes🎆)

<https://github.com/scottdurow/dataverse-ify>

***TESTS***
TS project can also provide us unit tests!
Instal jest package :

```powershell
npm install --save-dev jest ts-jest @types/jest
```

Create jest.config.js file with below code:

```js
module.exports = { preset: 'ts-jest', testEnvironment: 'node', roots: ['<rootDir>/tests'] };
```

Adjust package.json:

```json
{
  "devDependencies": {
    "@types/jest": "^30.0.0",
    "@types/xrm": "^9.0.88",
    "jest": "^30.2.0",
    "ts-jest": "^29.4.6",
    "ts-node": "^10.9.2",
    "typescript": "^5.9.3"
  },
  "scripts": {
    "build": "tsc",
    "watch": "tsc --watch",
    "test": "jest", 
    "test:watch": "jest --watch"
  }
}
```

Adjust tsconfig.json :

```json
{
    "compilerOptions": {
        "target": "ES5",
        "module": "commonjs",
        "outDir": "dist",
        "rootDir": "src",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "types": [
            "jest",
            "xrm"
        ],
    },
    "include": [
        "src/**/*"
    ]
}
```

Add tsconfig.test.json:
```json
{
    "extends": "./tsconfig.json",
    "compilerOptions": {
        "module": "commonjs",
        "types": [
            "jest"
        ],
        "esModuleInterop": true,
        "skipLibCheck": true
    },
    "include": [
        "tests",
        "src"
    ]
}
```

CreateYour test folder with test ts file :
![TestFolderAndFiles](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/TestFolderAndFiles.png)

```js
import AccountMainLib from "../src/account/account-main";

describe("hasCountryCode", () => { 
    it("returns true for +48", () => { 
        expect(AccountMainLib.hasCountryCode("+48123456789")).toBe(true); 
    }); 
    it("returns false for missing prefix", () => { 
    expect(AccountMainLib.hasCountryCode("123456789")).toBe(false); }); 
});
```
Run 
```powershell
npm test
```
![TestsPass](https://raw.githubusercontent.com/Ludwikster/thedataversegrimoire.github.io/main/images/TestsPass.png)

And You've done Your first unit tests!
