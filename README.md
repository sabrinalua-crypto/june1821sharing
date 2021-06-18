# The beauty of Makefiles #

## What is it##

Machine-readable documentation that makes workflow reproducible (especially locally!)

By going through a well-written Makefile, developers onboarding is able to trace the commands/flow in each step of the application.

## Example ##
Take for example a project with the following directory structure,

```
project
└───db
│   │   file1.sql
│   │   file2.sql
│   │   ...
│
│   docker-compose.yml
│   Makefile
│   package.json
│   index.js
│
└───src
    │   package/
    │   ...
```

where the contents of Makefile: 

```
ABSOLUTE_PATH:="$(shell git rev-parse --show-toplevel)"

.EXPORT_ALL_VARIABLES:

DB_USERNAME=postgres
DB_PASSWORD=postgres
DB_NAME=my_test_db
DB_HOST=localhost
DB_PORT=5432
DB_SSLMODE=disable

PHONY: .

run: dep
	npm start

test: clean
	npm test

setup_local_env:
	PASSWORD=${DB_PASSWORD} USER=${DB_USERNAME} PORT=${DB_PORT} docker-compose up -d

migrate_db:
	flyway -locations=filesystem:${ABSOLUTE_PATH}/db -user={DB_USERNAME} \
	-password=${DB_PASSWORD} -url=jdbc:postgresql://{DB_HOST}:{DB_PORT}/{DB_NAME} migrate

dep:
	npm install
	
buiid: dep
	npm build	
	
clean:
	@echo "clean"
	
```

Just by a quick glance through the makefile, one can:

*  deduce the resources required in the project via `setup_local_env`.
*  figure out:
	*  	 the db migration manager used in this project. in this case, `flyway`
	*  	 path of migration files
* know which env variables to set on other environments (if required)
	*	adding `-e` to the command tells the make to ignore makefile env vars and instead use system 		envs. Eg.:
	
		```
		make migrate_db -e
		```
		

## TLDR ##

### Pros ###
* collects all the scripts frequently used, in one location for easy reference
* serves as document of sort for a set of commands that you keep forgetting.
* 

### Cons ###
* works only on *nix systems
* recursive make files is quite complicated - but thats a story for another day
		


## FAQs ##
* manpage: `man make`
* 	readmore: https://gertjanvandenburg.com/files/talk/make.html



