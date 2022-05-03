# JIRA/GIT SAP Integration
## General Informations
- See the Guide at  https://github.com/SAP/styleguides/tree/main/abap-code-review
- Also Example by Lars Hvam https://github.com/abapGit/abapgit-review-example
- Important SAP BAdIs CTS_REQUEST_CHECK and CTS_IMPORT_FEEDBACK
- JIRA API Authentification https://developer.atlassian.com/cloud/jira/software/rest/intro/#introduction
  - We didn't got OAuth to work and used Basic Auth with a separate User and Token and a SM59 Destination with Proxy
  ```
    DATA lo_http_client TYPE REF TO if_http_client.
    cl_http_client=>create_by_destination ...
    DATA(lv_token) = cl_http_utility=>encode_base64( unencoded = |{ c_conf_jira_user }:{ c_conf_jira_token }| ).
    lo_http_client->request->set_header_field( name = 'Authorization' value = |Basic { lv_token }| ).
    ```
- abapGit should work with developer version
- We use GitLab but Github would also work  (we also integrate abapLint)
- check STRUST Certificates on Errors
    
## Transport Request Creation
- BAdI Implementation CTS_REQUEST_CHECK Method CHECK_BEFORE_CREATION
- Check if a JIRA ID is in the TR Text with substring/regex (for example #Project-001#)
- Check if a TR already exists for that Jira ID
- Check the Jira Task State with the API (https://developer.atlassian.com/cloud/jira/software/rest/api-group-issue/#api-rest-agile-1-0-issue-issueidorkey-get)
- optional write the TR Number back in a Jira Task Field

## Transport Task Release
- BAdI Implementation CTS_REQUEST_CHECK Method check_before_release
- Check Transporttype if Task
### Jira
- Check if Jira Task is still in state working
### SAP
- auto generate ToC if the last Task is released for a request (CALL FUNCTION 'TMW_CREATE_TRANSPORT_OF_COPIES')
### Git
- Check if Parent Packages are in abapGit
- Check if Merge Request / Branch still exists (otherwise create a Branch/Merge Request)
- Commit changes with Transport Task ID

## Transport Request Release
- BAdI Implementation CTS_REQUEST_CHECK Method check_before_release
- Check Transporttype if Request
### Jira
- Check if Jira Task is in state ready for deployment
### Git
- Check if Merge Request is approved
- Merge the Merge Request (auto delete Branch)

## Transport Request Import
- CTS_IMPORT_FEEDBACK (we have to switch SAP Client with a Function Module with SM59 Destination)
- Post Comment in Jira Ticket 

