# How-to-Customize-CaseAssist

## Useful Documentation

- [Use Case Assist With Secured Content](https://docs.coveo.com/en/na6a5281/service/use-case-assist-with-secured-content)
- [Case Assist Github](https://github.com/coveooss/sf-case-assist-cookbook)
- [Quantic Usage](https://docs.coveo.com/en/quantic/latest/usage/)

## Prerequesites

- VS Code
- [SFDX or newly named SF CLI](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup)
- [Salesforce Extension Pack for VSCode](https://marketplace.visualstudio.com/items?itemName=salesforce.salesforcedx-vscode)
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
sfdx project retrieve start --package-name 'Quantic'
```

If you can see the folders in our Project folder, you are now ready to start.

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

![new flow](https://github.com/pbdeblois/How-to-Customize-CaseAssist/blob/main/CaseAssist.png)

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

![deploy](https://github.com/pbdeblois/How-to-Customize-CaseAssist/blob/main/CaseAssist_1.png)

Repeat for the 3 others LWC and deploy.

## Reorganize our flow

Now that we have all the parts of the flow, we will swap the OOTB for ours.
Edit the first node. Drag and drop the newly created LWC below the original one. Give our new component a different API name and reenter the same values for the rest. Save and do so for the 3 others nodes
Note: On each node, we need to update the Advanced section.
- On the first Describe Problem, adjust "Data from the case" with {!caseData} and select "Refresh inputs to incorporate changes elsewhere in the flow"
- Do the same on Provide Details Screen
- One Review Resources screen, we will en {!recordId} in "The record ID of the newly created case"
- Then Final Screen, {!recordID} in "Newly created case ID"

Save the Flow as a new version and click on Activate.

## Community

We will be assuming you already set up a Community.
Access the Builder, and select the Page where you want to CaseAssist to appear. I suggest `Contact Support`. Remove the previous components and Drag&Drop a `Flow`. Click the Flow and choose the Custom Flow we created earlier. Fill in the information needed.
Publish and Test.

Thats it. We have our CaseAssist working with a copy of the OOTB LWC. Its not time to customize.

## Platform Token

First step will be to create an Apex Class where our platform token logic will be handled. 
CTRL+SHIFT+P to Create a new Apex Class and named it something similar to CustomCaseAssistController.
Copy the [file in the cookbook in there](https://github.com/coveooss/sf-case-assist-cookbook/blob/main/src/main/default/classes/CaseAssistController.cls).
If we leave it as is, we will query the SearchUISamples org. 
Follow the documentation [Use Case Assist With Secured Content](https://docs.coveo.com/en/na6a5281/service/use-case-assist-with-secured-content), comment the first part and uncomment the second.

Deploy to Org.

## Modify our interface

In the Quantic folder, you will find the LWC  `quanticCaseAssistInterface`. We want a copy of that. Proceeds the same way we did earlier. Give it a meaningful name eg `customQuanticCaseAssistInterface`
This time, we will customize the logic in there. First line should be the `import` of the `getHeadlessConfiguration` method. Change the file name from HeadlessController to our custom PlatformToken class.

Deploy to Org

## Modify our CaseAssist

In our own version of describeProblemScreen.html, we have a component being called. Change this to call our new custom Interface. 

```html
<c-quantic-case-assist-interface
  engine-id={engineId}
  case-assist-id={caseAssistId}
  search-hub={searchHub}
></c-quantic-case-assist-interface>

// Change it to this
<c-custom-quantic-case-assist-interface
  engine-id={engineId}
  case-assist-id={caseAssistId}
  search-hub={searchHub}
></c-custom-quantic-case-assist-interface>
```

Deploy to Org

Refresh your Case Assist and look at the token in the Headers. You should have a Platfom token now.
