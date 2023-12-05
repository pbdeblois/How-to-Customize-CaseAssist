# How-to-Customize-CaseAssist

## Useful Documentation

- [Quantic Usage](https://docs.coveo.com/en/quantic/latest/usage/)
- [SearchHub](https://docs.coveo.com/en/quantic/latest/usage/safely-set-search-hub/)

## Prerequesites

- VS Code
- [SFDX or newly named SF CLI](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup)
- [Salesforce Extension Pack for VSCode](https://marketplace.visualstudio.com/items?itemName=salesforce.salesforcedx-vscode)
- Auth the VSCode to your Org

## Setting up our Environment

First, we install the [Coveo For Salesforce Package](https://docs.coveo.com/en/1102/coveo-for-salesforce/install-the-coveo-for-salesforce-application) if not already done. Link it to your Coveo Organization.

Then, we can install Quantic.
To do so, [follow this link](https://docs.coveo.com/en/quantic/latest/usage/#install-quantic). Click the link to install in Production as Dev org are considered Prod.

We are ready to start importing the files we will need in VS Code.

Open VS Code.
Press CTRL+SHIFT+P and search for SFDX.
Create a Project and follow the instruction. This will create a new Folder. Once done, VSCode should open in that folder.
Now, CTRL+SHIFT+P again to "Authorize an Org" and follow the login procedures.
To start working on files, we will need them accessible in our VSCode.
Lets retrieve the QUantic Package with this command line

```sh
sfdx project retrieve start --package-name 'Quantic'
```

If you can see the folder in our Project folder, you are now ready to start.

## Create the APEX class

We will need 2 new classes in order to generate our custom token.

### TokenProvider
CTRL+SHIFT+P -> Create a new Apex Class -> Naming it `CustomCommunityQuanticToken`
This is where the logic applied to the search token will be generated.
We will keep it simple for now by applying our SearchHub, a simple filter and an additionalUserIdentities. Those can be dynamically changed.


```java
global with sharing class CustomCommunityQuanticToken implements ITokenProvider {
// https://docs.coveo.com/en/quantic/latest/usage/safely-set-search-hub/
// Global Class -> https://docs.coveo.com/en/1075/coveo-for-salesforce/globals-class#generatesearchtoken
    @AuraEnabled(continuation=true cacheable=false)
    global static String getHeadlessConfiguration() {
        Map<String, Object> coveoEndpointData = CoveoV2.Globals.getEndpointData();
        String searchToken = CoveoV2.Globals.generateSearchToken(new Map<String, Object> {
          'searchHub' => 'SecureSearchHub'    
          'filter' => '@source==KnowledgeBase',    
          'additionalUserIdentities' => new List<CoveoV2.UserIdentity>{
              new CoveoV2.UserIdentity('goku@planetvegeta.com','Email Security Provider','User')
          }
        });


        Map<String, String> headlessConfiguration = new Map<String, String>();
        headlessConfiguration.put('organizationId', (String) coveoEndpointData.get('organization'));
        headlessConfiguration.put('accessToken', searchToken);
        headlessConfiguration.put('platformUrl', (String) coveoEndpointData.get('clientUri'));
        return JSON.serialize(headlessConfiguration);
    }
}
```

### Headless Controller

This apex class will be in the middle of our QuanticSearchInterface and the TokenProvider. 
CTRL+SHIFT+P -> Create a new Apex Class -> Naming it `CustomCommunityHeadlessController`
Not much else will be happening here. Copy and paste the following in.

```java

global with sharing class CustomCommunityHeadlessController {
  /**
   * Get the configuration object (serialized) for the initialization of Coveo Headless through a class implementing `ITokenProvider`.
   * @return {String} A serialized object containing the following keys:
   *                      - `platformUrl`: the endpoint to contact Coveo.
   *                      - `accessToken`: a search token (see https://docs.coveo.com/en/56/build-a-search-ui/search-token-authentication).
   *                      - `organizationId`: the ID of the organization to query (see https://docs.coveo.com/en/148/manage-an-organization/retrieve-the-organization-id).\
   */
  @AuraEnabled(cacheable=true)
  public static String getHeadlessConfiguration() {

    return CustomCommunityQuanticToken.getHeadlessConfiguration();
  }
}
```

## Create the LWC

### QuanticSearchInterface
Lets start by copying where the QuanticSearchInterface component so we can start customizing our search token. CTRL+SHIFT+P to create a new LWC., name it customQuanticSearchInterface and copy each file from QuanticSearchInterface



### QuanticSearch
We will create a new QuanticSearch. CTRL+SHIFT+P to create a new LWC. Lets name it `customQuanticSearch`. Copy file by file the content of quanticSearch in our new component. 


## Reorganize our flow


## Community


## Custom Token

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
