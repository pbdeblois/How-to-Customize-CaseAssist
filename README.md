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

## Create the Flow

With CaseAssist being a LWC, we will need to set up a Flow. The Cookbook already provides 2 flows that we can use.

### Recommended Flow

1. Subject and Description
2. Classification Screen
3. Document Suggestion Screen
4. Confirmation Screen

### Demo Flow

1. Subject and Description
2. Document Suggestion Screen
3. Confirmation Screen

We will use the Recommend Flow as we want the whole process. Navigate to the Setup menu of Salesforce and then, search `Flow` in Quick Find. Click on `Flow` then Recommended Flow.
Top Right Corner, you will see a `Save As`. Select this and save in a new flow.

![[Training - How to Customize CaseAssist.png]]
=======

We have now a flow that we can customize. This flow uses 4 LWCs in our package.

- describeProblemScreen
- provideDetailsScreen
- reviewResourcesScreen
- finalScreen

Lets put that Flow on the side for now.

## Create the LWC

In order to test customization and OOTB component, our main goal will be to duplicate the LWC. This means we will always have a original component to refer too if needed.
Lets create our duplicate of the main components.
Open VSCode and enter CTRL+SHIFT+P -> SFDX Create Lightning Web Component
Give it a meaningful name e.g. `customDescribeProblemScreen`
once created, we can copy the file that are in `describeProblemScreen` to our newly created `customDescribeProblemScreen` .
While copying the JavaScript file, don't forget to keep the name of the class.

```js
export default class DescribeProblemScreen extends LightningElement {
// Will become
export default class CustomDescribeProblemScreen extends LightningElement {
```

Don't forget to add a .css file with the same name.
In the .xml file, change the value of the `masterLabel`. Giving it the same name as the component is fine.

Once everything is copied and the names have been changes, Deploy to org

![[Training - How to Customize CaseAssist_1.png]]

Repeat for the 3 others LWC and deploy.
