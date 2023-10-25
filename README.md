# ip2m-metrr-public

A public repository making the ASU developed IP2M-METRR tool available for limited public sector use.

Please review the details of the LICENSE file included in this repository.

## Support

Please reach out to <support@pars.doe.gov> for installation and configuration assistance. Within PARS, the tool is hosted in an Azure Web Apps container connecting to an Azure SQL Managed instance, but other Docker hosting tools should work. The following Environment Variables will need to be configured on your Docker host. Comments are provided after lines that need to be modified.

```{conf}
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

## Other Technical Notes

The following sections provide for some general notes and tips for composing the Docker container and running the application. They are provided "as-is", and may not work depending on your system configuration. If you encounter issues, please reach out to PARS Support at <support@pars.doe.gov>.

### Pre-requisites

1. Install [Docker (Docker desktop for mac/windows)](https://docs.docker.com/get-docker/)
2. Install [docker-compose](https://docs.docker.com/compose/install/). Note: only do this for Linux. Mac and Windows install compose in the first step

### Start app and debug

Start the app services using the following command.

    docker-compose up -d --build --remove-orphans

Live reload is enabled for both flask and react for easier development. For logs, use the command

    docker-compose logs -f

Shut down server and remove all data

    docker-compose down -v

### Using the app

When the server starts, the following users are created by default. \<email>:\<password>

    admin@ip2m.com:Admin@123
    facilitator@ip2m.com:Facilitator@123
    user@ip2m.com:User@123

The first user has super user access and has CRUD access to users.

### Production deloyment

Run prod containers(gunicorn, nginx) using docker-compose. Use sudo if needed.

    docker-compose -f docker-compose.prod.yml up -d --build --remove-orphans

### EOL issues with Windows

After cloning and running the docker compose up command if you see the line below in the docker logs, Then change the line endings of the `./server/entrypoint.sh` or `./client/entrypoint.sh` to linux line endings (LF). Then the container will run the script files.

```
standard_init_linux.go:228: exec user process caused: no such file or directory
```

### Issues with the flask server alembic scripts

If the flask server stops working. Delete the versions folder in `./server/migrations. Try the steps below to restart the flask server

```docker
docker-compose down -v
docker volume rm $(docker volume ls -q)
```

Then try running the docker compose up to start all the containers

```
docker-compose up -d --build --remove-orphans
```

### Using with WSL 2 on Windows

Live reload does not work with windows file system and therefore it is recommended to use [WSL 2](https://docs.docker.com/desktop/windows/wsl/) to work around that.
In addition, you may need to change the permission settings when you get [permission denied error](https://stackoverflow.com/questions/69579200/unable-to-run-docker-compose-from-wsl-2-ubuntu/69579356###69579356) using the following from your WSL distro (Ubuntu usually) terminal.

```
chmod +x deployment_scripts/entrypoint.sh
chmod +x server/entrypoint.sh
chmod +x client/entrypoint.sh
```

Following posts may help fix the issues that you may encounter.
<https://stackoverflow.com/questions/69578573/live-reload-does-not-work-in-docker-container-for-windows>
<https://stackoverflow.com/questions/69579200/unable-to-run-docker-compose-from-wsl-2-ubuntu>
Note: This would make your PC really slow, such that even editing this readme and saving it would hang it and maky take a minute or more.

### Running Test cases

Start the test app service using the following command.

```
docker-compose -f docker-compose.test.yml up -d --build --remove-orphans
```

All the test cases present in the project will run by using the above command and displays test summary information with  
how many test cases were passed and failed.

To get a test report on a single file, open the docker CLI command prompt and run the following command,

```command line
pytest <path_to_file>/<file_name>.py
```

Example

```command line
pytest tests/views/test_auth.py
```

If we want to run a specific method in a test file, use the following command in docker cli

```commandline
pytest <path_to_file>/<file_name>.py::<method_name>
```

Example

```commandline
pytest tests/models/test_user.py::test_add_user
```
