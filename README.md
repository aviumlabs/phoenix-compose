# A Phoenix Framework Docker Compose Repo


---


This is a template repo and it can be utilized using the following pattern. 


## Creating a Repository From this Template


[GitHub Documentation] [git-from-template]


### Example Project


#### Create a New Repository on GitHub.com


- Go to https://github.com/aviumlabs/phoenix-compose
- Select Use this template
- Select Create a new repository


Repository name: __<project_name>__  
Description: __project_description__  
Public  


- Select 'Create repository from template'


Generating your repository...


### Create and Clone a New Repository with GitHub CLI


    gh repo create <application_name> -c -d "Application description" \
    --public|private -p aviumlabs/phoenix-compose 


Created repository \<github\_userid\>\<application\_name\>  on GitHub  
Cloning into '\<application\_name\>'...  


---


### Avium Labs Prepare Script


The included prepare script will create a Phoenix Framework project. 


Run `./prepare -h` to get started.


**Note: docker daemon must be running before running the prepare script.**


#### Initailize the Application Setup


Running the prepare script to initialize the Phoenix Framework project:


    $ cd <application_name> 

    ./prepare -i <application_name> 


>
> Initializing Phoenix Framework project...  
> Application container root............... /opt  
> Application name......................... \<application\_name>  
> Running phx.new...  
>
> We are almost there!  
>


#### Finalize the Application Setup


    ./prepare -f


>    
> Running mix ecto.create...  
> 
> ...  
> 
> Running docker compose up; press ctrl-c to stop.  
> 
> ...  
> 


The application is now running in the foreground and can be shutdown 
with `ctrl-c a`.


## Service Details


The Phoenix Framework application is exposed on port 4000 (default). 


The src directory in the project working directory is bind mounted to the 
APP\_CONTAINER\_ROOT directory which by default is set to /opt when initialized 
and is then set to /opt/\<application\_name\> after running finalize.  

The default APP\_CONTAINER\_ROOT can be set during the project initialization 
phase by specifying the -r flag to the prepare script.  


    ./prepare -i <application_name> -r /app


The prepare script performs the following actions during initialization:  
- generates a docker environment file (.env)  
- generates a random password for the Postgres account  

The prepare script performs the following actions during finalization:  
- prepares the config/dev.exs and config/test.exs to run in docker:  
  - sets the ip address to 0, 0, 0, 0  
  - sets the database host  
  - sets the database password (pulled from the .secret\_db file)


### Foreground or Background Services


By default aviumlabs/phoenix-compose brings up services in the foreground. To 
run the services in the background, stop the currently running services:


    $ ctrl-c

    docker compose up -d

    
To stop an individual service:


    docker compose stop [app, db]


To start an individual service:


    docker compose start [app, db]


To view the the logs of a background service:


    docker logs -f <running_container_name>


To list the current running containers:


    docker container ls


| CONTAINER ID   | IMAGE                           | ... | NAMES                        |
|----------------|---------------------------------|-----|------------------------------|
| nnn            | aviumlabs/phoenix:latest-alpine | ... | \<application\_name\>-app-1  |
| nnn            | postgres:16.3-alpine3.20        | ... | \<application\_name\>-db-1   |


### Running ecto.reset


If the services are running in the foreground; you need to stop the running 
services `ctrl-c` and then run the following:


    docker compose up db

    mix ecto.reset

    ctrl-c

    docker compose up


Alternatively, if the docker services are running in the background;


Then run the above steps as follows:


    docker compose stop app

    mix ecto.reset

    docker compose start app


### Docker Images


- Phoenix Framework image: aviumlabs/phoenix:latest-alpine (Phoenix 1.7.14)
- PostgreSQL image: postgres:16.3-alpine3.20


## Umbrella Project


A Phoenix Framework umbrella project can also be created with the prepare 
script. 


    ./prepare -i <application_name> -r /opt -u

    ./prepare -i <application_name> -u


Official Elixir Umbrella Documentation 


[Link](https://elixir-lang.org/getting-started/mix-otp/dependencies-and-umbrella-projects.html)


## Development


With the **src** directory bind mounted to the application directory, you can use 
your favorite local development environment to continue with developing 
your application.


### Running Mix and Iex 


To run mix or iex against the container, setting up some aliases can reduce some 
typing.

An initial alias file `.appdev` is provided to set a few aliases for running 
mix and iex in docker.


    alias mix="docker compose exec app mix"
    alias iex="docker compose exec app iex -S mix"


The following exports and aliases can be added to support development under 
the umbrella app model.


    export APP_CONTAINER_ROOT=/opt
    export APP_NAME=<app_name>

    alias amix="docker compose exec -w "$APP_CONTAINER_ROOT/$APP_NAME"_umbrella/apps/$APP_NAME app mix"
    alias wmix="docker compose exec -w "$APP_CONTAINER_ROOT/$APP_NAME"_umbrella/apps/"$APP_NAME"_web app mix"


Then before starting development, source the file in your shell:


    $ cd <app>/<root>

    . ./.appdev
   

Confirm the aliases are set correctly:


    alias

> 
> iex='docker compose exec app iex -S mix'
> mix='docker compose exec app mix'
>


#### Breakdown of the aliases


* docker compose exec is the docker syntax for executing a command in a container.
* app is the container to execute the command in.
* mix/iex is the command to execute.


[git-from-template]: https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template
