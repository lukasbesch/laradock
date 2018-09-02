# Laradock for Bedrock

This is a fork of [Laradock](https://github.com/laradock/laradock) prepared to work with WordPress boilerplate [Bedrock](https://github.com/roots/bedrock).

## What is different?
 - Document root points to /var/www/web
 - MySQLi extension is enabled by default
 - WP-CLI container
 - Composer container
 - ./laradock.sh helper script
 
## Installation

1. Clone this repository into a subdirectory of your Bedrock installation. If you want to stay up to date, you might want to use git submodules or git-subrepo.

       git clone https://github.com/lukasbesch/laradock-bedrock.git docker
       // or
       git submodule add https://github.com/lukasbesch/laradock-bedrock.git docker

2. (Optional) Copy the `env-example` to `.env` in your docker folder. Checkout its options and adjust them as needed.
3. In your project's `.env` file Set the `DB_HOST` constant to the corresponding container `mariadb` or `mysql`

       DB_HOST=mariadb
4. Start the containers

       ./laradock.sh up
5. Install Composer Packages

       ./laradock.sh composer install
       
Your site should be available at [http://localhost](http://localhost).

You could also use dnsmasq on your local machine to route all traffic with the TLD *.docker to localhost. Now you are able to give each of your sites a custom hostname such as [http://mysite.docker](http://mysite.docker). This is very convenient for the use with password managers. 

## Documentation   
Please checkout the official docs: https://github.com/laradock/laradock

## laradock.sh Commands ##

    create                      Creates DB and docker compose.
    up                          Runs docker compose.
    down                        Stops containers.
    ssh [--root]                Opens bash on the workspace, optionally as root user.
    wp [command]                Runs WP-CLI in one off container.
    composer [command]          Runs Composer in one off container.
    theme composer [command]    Runs Composer in theme directory.
    -- [command]                Executes any command in workspace.
    
Example:
    
    ./laradock.sh create
    ./laradock.sh composer install
    ./laradock.sh wp rewrite flush


## Roadmap

- [ ] `dnsmasq` instructions

- [ ] Setup script with user input to set variables

- [ ] Multi-Site Hosting

- [ ] Ansible Deployments
