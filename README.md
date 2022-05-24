# nws-unlock

## Note

This form/tool was created with Sapien PowerShell Studio 2022, version 5.8.206.0

## Purpose

New World Enterprise Security does not currently allow for the granular permissions so that a site admin can unlock a user account. With CJIS requirements, accounts must be locked after 5 invalid attempts, and stay locked until an admin unlocks the account. So for this purpose, I have put together a tool to specifically change this flag in the SQL Database until New World has the granular permissions needed to do this within their software.


## Limitations and Concerns

- [ ] Because this is directly interacting with the SQL Database, there are concerns regarding audit trails or potential misuse of the program to try and access privileged New World accounts
- [ ] While there are limited Select and Update permissions on the SQL User, it's still a lot of information and can be misused by others


## SQL Backend Prep Work

- [ ] Create a dedicated SQL User that only has access to specific columns in a specific database in order to see relevant Login IDs and ORIs to determine who is currently locked

```
CREATE LOGIN USERNAME WITH PASSWORD = 'PASSWORD';
use NWSEnterpriseSecurity
create user USERNAME for login USERNAME;
grant select on dbo.Users(LoginID,DefaultORI,LockedOutFlag,InactiveFlag,ModifiedDate) to USERNAME
grant update on dbo.Users(LockedOutFlag) to USERNAME
```

## Goals and Ideas for this Project

- [ ] For the duration this project is specific to our County, the server list will be pre-filled to reduce user error.
- [ ] Textboxes for the SQL Username and Password. Possibly hard-coded in later versions and create ORI-Specific applications. The reasoning behind potential hard-coded is that with each application update, the SQL password will be changed, thus requiring those site admins to get the newest version from IT so that old versions are not sitting around with potential bugs being used on production.
- [ ] Check for SQL connectivity before querying information
- [ ] Current query grabs all users that are locked out (and still active in the system, inactive users are filtered out), among all ORIs. Later versions potentially made for different site admins will have the ORI filter baked into the application so that they only see their own ORI
- [ ] If no users are currently locked out, notify user
- [ ] If a different server is selected from the dropdown, clear out current data and require connection test, then query locked out users before allowing unlock
- [ ] If the locked out user dropdown is blank, notify user
- [ ] Have a text file log that loads on application open and populates output textbox. If no log file exists, create it and log output to it with date, time, and actions taken