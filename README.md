A Phoenix Framework Docker Compose Repo
=======================================

This is a template repo and it can be utilized using the following pattern. 

Creating a Repository From this Template
----------------------------------------

[GitHub Documentation] [git-from-template]


### Example Project

- Go to https://github.com/aviumlabs/phoenix-compose
- Select Use this template
- Select Create a new repository

Repository name: gutentag
Description: A hello world application
Public

- Select Creat repository from template

Generating your repository...

#### Clone the new repository

GitHub CLI

    $ cd <project/home/directory>
    $ clone aviumlabs/gutentag

#### Aviumlabs Prepare Script

The included prepare script will create a Phoenix Framework project. 

Run `$ ./prepare -h` to get started.

Running the prepare script to initialize the Phoenix Framework project:

    $ cd gutentag
    $ ./prepare -i gutentag
...
We are almost there! ...

    $ ./prepare -f
    
Running mix ecto.create...
...
Running docker compose up; press cntl-c a to stop.
...


The example application is now running in the foreground and can be shutdown 
with `cntl-c a`.

Service Details
---------------

The Phoenix Framework application is exposed on port 4000. 
The src directory in the project working directory is bind mounted to the 
APP\_CONTAINER\_ROOT directory which by default is set to /opt.

The default APP\_CONTAINER\_ROOT can be set during the project initialization 
phase by specifying the -r flag to the prepare script.

    $ ./prepare -i gutentag -r /usr/local

The prepare script performs the follow functions:
- generates a docker environment file (.env)
- generates a random password for the Postgres account 
- prepares the config/dev.exs dev environment:
  - sets the ip address to 0, 0, 0, 0
  - sets the database host
  - sets the database password 


### Docker Images
- Phoenix Framework image: aviumlabs/phoenix:latest-alpine 
- PostgreSQL image: postgres:15.2-alpine 



[git-from-template]: https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template
