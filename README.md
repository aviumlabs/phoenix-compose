# A Phoenix Framework Rapid Development Environment

This project is designed to be a rapid development environment based on 
the amazing work of [Phoenix Framework](https://www.phoenixframework.org), 
[Elixir](https://elixir-lang.org/), [Erlang](https://www.erlang.org/), 
[Alpine Linux](https://www.alpinelinux.org/), and 
[PostgreSQL](https://www.postgresql.org/).

---

The repository is setup as a GitHub template repository, designed to be cloned 
into a new project to begin immediate development. 

The stack is as follows as of 2026-01-06:

* Alpine Linux 3.23.x  
* Erlang 28.1.x  
* Elixir 1.19.x  
* Phoenix Framework 1.8.x   
* PostgreSQL 18.x  

After creating a new project based on this repository, the directory layout is 
as follows:

>
> `project_name`/  
>> LICENSE  
>> README.md  
>> compose.yaml  
>> src/  
>> .env  
>> .appdev  
>  



The name of the parent directory is the same as the name of the repository 
created when running `gh repo create` command. 

See Creating a Repository From this Template below.  


The src directory is empty until `docker compose up` is run. On the first run 
of `docker compose up`, the src directory is bind mounted into the Phoenix 
Framework application (app) container. 


The Phoenix Framework phx.new command is automatically run and the src 
directory is populated with the Phoenix Framework application. The 
configuration files dev.exs and test.exs are automatically updated to support 
the docker runtime. These files are also automatically updated to support 
connecting to the included PostgreSQL database (db) container. 


Prior to running `docker compose up` the first time, one file must be 
generated - the .secret_db file containing the database connection 
credential.


Various shell scripts that can be used to generate the database secrets file. 

**macOS**
```shell
date +%s | shasum -a 256 | base64 | head -c14 > .secret_db
```

**linux-gnu**
```shell
date +%s | sha256sum | base64 | head -c14 > .secret_db
```

**Windows**
```PowerShell
$DB_SECRET_FILE = "$pwd\.secret_db"

# Generate a random password of length PwLength, defaults to 14 characters
function New-RandomPassword {
    param (
        [int]$PwLength = 14
    )
    # Char 97 - 122 'a-z'
    # Char 65 - 90 'A-Z'
    # Char 48 - 57 '0-9'
    # Char 45 '-'
    # Char 43 '+'
    $char_list = [char]97..[char]122 + [char]65..[char]90 + [char]48..[char]57 `
    + [char]43 + [char]45

    # Select a random character from the list and convert back to character
    $pass = -Join ((Get-Random -InputObject $char_list -Count $PwLength) | ForEach-Object {[char]$_})
    
    return $pass
}

# Write the database secret file
function Write-DbSecret {
    $line1 = Get-RandomPassword
    
    $line1 | Out-File -FilePath $DB_SECRET_FILE -NoClobber
}

# Script Driver
function main {
    Write-DbSecret
}

main
```


## Creating a Repository From this Template

General command syntax:  
```shell
gh repo create <application_name> -c -d "Application description" \
--public|private -p aviumlabs/phoenix-compose 
```

Specific example:  
```shell
gh repo create myapp -c -d "MyApp Test Application" --private -p aviumlabs/phoenix-compose
```


>  
> Created repository <github_userid>/myapp  on GitHub  
> https://github.com/<github_userid>  
> Cloning into 'myapp'...  
>  


## Initializing Your Phoenix Framework Application

After creating a new project through `gh repo create` and creating the 
database secret file, run `docker compose up`. 

The included .env file sets the values required for initializing the 
phoenix container and the password of the database.  

The default APP_NAME variable in the .env file is set to `app` and can be 
changed as needed.  


```shell
docker compose up  
```

>  
> app-1  | Updating dev.exs...  
> app-1  | Compiling 15 files (.ex)  
> app-1  | Generated app app  
> app-1  | The database for App.Repo has been created  
> app-1  | Updating test.exs...  
> ...
> app-1  | [info] Running AppWeb.Endpoint with Bandit 1.10.1 at 0.0.0.0:4000 (http)  
> app-1  | [info] Access AppWeb.Endpoint at http://localhost:4000  
> app-1  | [watch] build finished, watching for changes...  
> app-1  | = tailwindcss v4.1.12
> app-1  |  
> app-1  | /*! daisyUI 5.0.35 */ 
> app-1  | ...  
> app-1  | Done in 331ms.  
>  

Open http://localhost:4000 to the default landing page of a 
Phoenix Framework application.

The src directory is now populated with the application files and can be 
edited with your favorite editor.  

* README.md  
* assets/  
* deps/  
* _build/  
* config/  
* lib/  
* mix.exs  
* mix.lock  
* priv/  
* test/  

Edit src/lib/my_webapp/controllers/page_html/home.html.eex

Search for Phoenix Framework and insert some text like 
MyApp powered by Phoenix Framework.

Save the file and go back to your browser and you will automatically see 
the change. 

### Add a Project Dependency

Edit src/mix.exs and add the dependency to the dependency section:

```
defp deps do
    [
    ...
    ]
end
```

## .appdev

The `.appdev` file has two aliases for running mix and iex in the app container.

Source the .appdev file:  
```shell
. ./.appdev
```

Run mix to get the dependency:  
```shell
mix deps.get
``` 

## Application Testing

Edit the `.env` file and change the MIX\_ENV variable.

`MIX_ENV=test`  

Run docker compose down/docker compose up to load the updated configuration 
and then run `mix test`.  

Change the MIX\_ENV setting back to `dev`, run docker compose down/up to go 
back to development mode.


## Additional Docker Commands

Running the docker containers in the background:

```shell
docker compose up -d
```
   
To stop an individual service:

```shell
docker compose stop [app, db]
```


To start an individual service:

```shell
docker compose start [app, db]
```


To view the the logs of a background service:

```shell
docker logs -f [app, db]
```


To list the current running containers:

```shell
docker container ls
```


| CONTAINER ID   | IMAGE                           | ... | NAMES              |
|----------------|---------------------------------|-----|--------------------|
| nnn            | aviumlabs/phoenix:latest-alpine | ... | myapp-app-1        |
| nnn            | postgres:17-alpine3.22          | ... | myapp-db-1         |


## Resetting the Database

### Run ecto.reset

If the services are running in the foreground; you need to stop the running 
services `ctrl-c` and then run the following:

```shell
docker compose up db
```

```shell
mix ecto.reset
```

>  
> ...  
>  

```shell
ctrl-c
```

```shell
docker compose up
```

Alternatively, if the docker services are running in the background;

Then run the above steps as follows:

```shell
docker compose stop app
```

```shell
mix ecto.reset
```

```shell
docker compose start app
```

[GitHub Template Reference](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)