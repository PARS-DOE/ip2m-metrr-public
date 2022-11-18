# ip2m-metrr-public
A public repository making the ASU developed IP2M-METRR tool available for limited public sector use

Please review the details of the LICENSE file included in this repository.

### Server Based Docker Image

A docker image for a server-installable version of the IP2M-METRR tool is available in a public docker registry at parspublic.azurecr.us/ip2m-server:11.22.0.

This file can be accessed using the docker command below:

```
docker pull parspublic.azurecr.us/ip2m-server:11.22.0
```

Please reach out to support@pars.doe.gov for installation and configuration assistance. Within PARS, the tool is hosted in an Azure Web Apps container connecting to an Azure SQL Managed instance, but other docker hosting tools should work. The following Environment Variables will need to be configured on your docker host. Comments are provided after lines that need to be modified.

```
FLASK_APP=project/__init__.py
FLASK_ENV=production
FLASK_DEBUG=0
PYTHONDONTWRITEBYTECODE=1
STATIC_URL = "/static/"
STATIC_ROOT=/usr/src/app/project/build/static
PYTHONUNBUFFERED=1
ORIGINS=*
UPGRADE_FLAG=1
VERSION=2
SQL_HOST=ip2m-db-prod                                                                        #Replace with name of your SQL server
SQL_PORT=1433                                                                                #Replace with port of your SQL server
DATABASE=IP2M
DB_URI=mssql+pymssql://sa:password@ip2m-db-prod:1433/IP2M?charset=utf8                       #Replace with database connection string including account info
MAIL_USERNAME=""                                                                             #Replace with mail server username
MAIL_PASSWORD=""                                                                             #Replace with mail server password
SAML_METADATA_URL=https://dev-78194679.okta.com/app/exk3vxk5aoOb726S35d7/sso/saml/metadata   #Replace with url containing SAML metadata for single sign on
APP_URL=http://localhost:8001
INVITATION_EXPIRY_HOURS=24
ELK_ENABLED=0
LINK_INVITE_APPROVALS_REQUIRED="USER, ADMIN"
```
