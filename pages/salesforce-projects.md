## Professional Projects

> The projects outlined below were completed and delivered during my career as a developer. The following Salesforce specific acronyms are used: Salesforce Object Query Language (SOQL), Salesforce Object Search Language (SOSL) and Lightning Web Component (LWC).

## Salesforce-NetSuite Integration
> Batchable and Schedulable Apex, Triggers, SOQL, NetSuite, OAuth

**Task**: Integrate NetSuite with Salesforce using the Apex Batchable and Schedulable interface, replacing an existing third-party integration tool in use that is inefficient.

**Solution**: First I set up relevant NetSuite configurations; an integration record for Salesforce, an access token, an RSA certificate and relevant user permissions. The token and certificate keys were stored in custom metadata in Salesforce. A method was put in place to fetch this metadata and add an OAuth header to each callout.

Next, I created endpoint mapping records, containing endpoint URL, HTTP method, expected response code, and sample JSON bodies for request and response. A NetSuite Id custom field was added to the account, product and user standard objects. 

I then updated the opportunity trigger to queue an Apex batch for newly 'Closed Won' records. This batch creates customers in NetSuite from account records associated with the opportunity, populates the NetSuite Id field, and subsequently creates a customer-subsidiary relationship for each custom Salesforce subsidiary object record. 

Two schedulable classes were created. The schedulable classes queue a batch for the user and product objects respectively. The Salesforce user object corresponds to the NetSuite employee object, and product to service sale item. Each batch queries the Salesforce objects using SOQL, and the corresponding NetSuite objects using the SuiteQL endpoint. Matching by email for users, and product code for products, Salesforce records are matched with corresponding NetSuite objects, and the NetSuite Id field on the Salesforce records is updated. Any errors are tracked and logged into a custom log record in Salesforce. Governor limits are respected by batch size and DML operations.

**Outcome**: The client found no errors during acceptance testing and replaced the third-party tool with the provided solution, completing an £18,000 project. Data is now efficiently synchronised between Salesforce and NetSuite.

## Reconciliation Lightning Web Component 
> LWC, Apex, JavaScript, HTML, SOQL, Approval Processes

**Task**: Allow finance agents to track bank account balances and payment transaction amounts across ledgers to ensure amounts match across systems. If they do not match, provide the functionality to investigate.

**Solution**: I created a reconciliation custom object in Salesforce; a header record providing a summary of the child reconciliations for the day (one child for payment reconciliations, and one for account balances), as well as an Apex REST API endpoint. A HTTP PUT request is made to the endpoint from the backend nightly to create a reconciliation object for the day’s transactions and balances. Next, I made a custom reconciliation viewer Lightning Web Component. Two lightning inputs allow for a date range to be selected. Using a SOQL query and applying pagination to the results, reconciliation records are displayed in a lightning datatable. Upon selecting a reconciliation record, child reconciliations are fetched from the backend REST API using a HTTP GET request. The children are also displayed in lightning datatables, presented below the parent datatable in a lightning tabset. All reconciliations have a row action to get further details, and child reconciliations have two more row actions, allowing agents to change payment/balance reconciliation status and escalate via HTTP PATCH callouts. I also put an approval process in place, allowing for reconciliation of header records after all children are escalated or reconciled. This LWC is displayed in two locations: on a lightning page allowing access to all header records, and also on each header record page, preselecting the current record. 

**Outcome**: I ran a training session and demonstration for the Chief Technical Officer, Chief Operating Officer, Operations Manager and operations agents. Following this training session, daily reconciliations across the bank are now carried out using this tool, replacing the previously used Excel spreadsheets, improving accuracy and efficiency. I proceeded to implement two similar viewers, a transaction viewer and a fee collection viewer. All three viewers are used daily by finance and operations agents.

## Account Merge Lightning Web Component 
> Apex, JavaScript, HTML, SOQL, SOSL

**Task**: The ability to merge any two accounts of the same record type was required as a custom component.

**Solution**: I created a quick action LWC on the account record page, with an account selection stage and field-value selection stage. The account selection stage takes text (partial or full account name) as input, executes a wildcard SOSL query for accounts partially matching the input name and record type, and displays results with preview information (type, owner, created date). After selecting an account to merge with, account fields are dynamically retrieved from schema. Fields that are updateable, accessible and not calculated are processed. Iterating over each field, an Aura Enabled “comparison entry” class is populated with the field name, parent values, child values, and alternate values. Parent values are any values stored in the current field on the source account, and child values are the same but from the selected account. If the current field is of display type Phone or Email, all other field values of the same data type (that are not already stored in parent or child values) are fetched and stored as alternate values. Lookup field values are converted from record ids to names (fetched dynamically via the object’s standard name field). Null values are displayed to the user as “[Clear Field]” and can be toggled visible if values both are null or matching. I created a “Greengage Datatable” component, extending lightning datatable. This was used for displaying the comparison entries, specifically for alternate values to display multiple radio buttons in a single cell. By default, all parent values are selected. Once the user has confirmed the selected field-value pairs, values are dynamically cast to the appropriate data type (via a display type to data type map), inserted into the parent account, and the merge DML operation is applied.

**Outcome**: Agents with su cient permissions can now merge any two accounts of matching record type across the organisation, with the ability to select from parent, child and alternate values.
