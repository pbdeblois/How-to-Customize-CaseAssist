# How-to-Customize-CaseAssist


## Useful Documentation
- [Use Case Assist With Secured Content](https://docs.coveo.com/en/na6a5281/service/use-case-assist-with-secured-content)
- [Case Assist Github](https://github.com/coveooss/sf-case-assist-cookbook)
- [Quantic Usage](https://docs.coveo.com/en/quantic/latest/usage/)

## Prerequesites
- VS Code
- SFDX or newly named SF CLI
- Auth the VSCode to your Org

## Setting up our Environment
First, we install the [Coveo For Salesforce Package](https://docs.coveo.com/en/1102/coveo-for-salesforce/install-the-coveo-for-salesforce-application) if not already done. Link it to your Coveo Organization.

Then, we can install Quantic.
To do so, [follow this link](https://docs.coveo.com/en/quantic/latest/usage/#install-quantic). Click the link to install in Production as Dev org are considered Prod. 
Lets look at the URL of that link, we will need it to install CaseAssist CookBook in an easier way than the CLI command. 
-> https://login.salesforce.com/packaging/installPackage.apexp?p0=04t6g000008b4BzAAI
After the `=` sign, we can see the package ID. Simply by changing this to the CookBook, we can install it without the need of SFDX.

Lets navigate to this link -> [Salesforce CaseAssist CookBook](https://github.com/coveooss/sf-case-assist-cookbook#3c-install-the-app-using-an-unlocked-package)
In the CLI command, we can see the package ID. At the time of writing those line, the ID is `04t3s000003X61EAAS` . 
Now lets build the URL and click it! -> 
https://login.salesforce.com/packaging/installPackage.apexp?p0=04t3s000003X61EAAS

We are ready to start importing the files we will need in VS Code.

Open VS Code.
Press CTRL+SHIFT+P and search for SFDX.
Create a Project and follow the instruction. This will create a new Folder. Once done, VSCode should open in that foler.
Now, CTRL+SHIFT+P again to "Authorize an Org" and follow the login procedures.
To start working on files, we will need them accessible in our VSCode.
Lets retrieve the CaseAssist with this command line
```sh
sfdx project retrieve start --package-name 'Salesforce Quantic Case Assist Cookbook'
```
If you can see the folder in our Project folder, you are now ready to start.
