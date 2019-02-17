# Laradock for Bedrock

This is a fork of [Laradock](https://github.com/laradock/laradock) prepared to work with WordPress boilerplate [Bedrock](https://github.com/roots/bedrock).

## What is different?
 - Document root points to /var/www/web  
 - xDebug enabled  
 - WP-CLI and Composer installed in workspace container
 - WP-CLI and Composer one-off container
 - Additional PHP UTF-8 Locales are installed (de_DE, es_ES, fr_FR)  
 - **./laradock.sh CLI tool** (that also uses docker-sync)
 
 [» Compare the fork on Github](https://github.com/laradock/laradock/compare/master...lukasbesch:master)
 
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

### Global installation

You add this to your `.bashrc` or `.zshrc` to create a global command that runs the `laradock.sh` in subdirectory:

    laradock() {
        FOUNDSCRIPT=$(find . -name laradock.sh -maxdepth 2 | sed "s|/[^/]*$||");
        echo "Using: $FOUNDSCRIPT/laradock.sh";
        pushd $FOUNDSCRIPT > /dev/null
        sh -c "./laradock.sh $@"
        popd > /dev/null
    }

### Custom hostnames (Hosts file)

Update your hosts file like that:

    127.0.0.1 yoursite.docker

### Custom hostnames (dnsmasq)

You can also use dnsmasq on your local machine to route all traffic with the TLD *.docker to localhost. Now you are able to give each of your sites a custom hostname such as [http://yoursite.docker](http://yoursite.docker). This is very convenient for the use with password managers. 

**These instructions are for macOS:**

If you don't have [Homebrew](https://brew.sh/) installed yet, do it :-)

    # This might be outdated, check https://brew.sh
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

Once you have it installed, you can use it to install Dnsmasq fairly easy:

    # Update your homebrew installation
    brew up
    # Install dnsmasq
    brew install dnsmasq

Please follow the installation instructions and run the commands `brew` tells you to.

#### Configuring dnsmasq

Dnsmasq should be installed and running now, so now we can use it to resolve certain dns request patterns to ips.
Edit the `/usr/local/etc/dnsmasq.conf` and add the following line:

    address=/docker/127.0.0.1

You should add it near `address=/double-click.net/127.0.0.1` to keep the file consistent.

Now restart dnsmasq to apply the changes you've made.

    sudo launchctl stop homebrew.mxcl.dnsmasq
    sudo launchctl start homebrew.mxcl.dnsmasq

To test if everything is set up correctly, use the `dig` command against your new DNS server:

    dig testexample.docker @127.0.0.1

You should get back something like this:

    ;; ANSWER SECTION:
    testexample.docker. 0 IN	A	127.0.0.1

#### Configuring macOS

We want to use our new DNS server only for development purposes. So the best approach is to send only `*.docker` queries to it.
You have to create the resolver directory.

    sudo mkdir -p /etc/resolver

Then create a file in this directory with the same name of your top level domain (`docker`)

    sudo tee /etc/resolver/docker >/dev/null <<EOF
    nameserver 127.0.0.1
    EOF

Once you’ve created this file, it will automatically be read.
To test if everything is set up correctly, use the `ping` command:

    # Make sure you haven't broken your DNS.
    ping -c 1 www.google.com
    # Check that .docker names work
    ping -c 1 example1.docker
    ping -c 1 subdomain.xyz.docker

You should see results that mention the IP address in your Dnsmasq configuration like this:

    PING example1.docker (127.0.0.1): 56 data bytes

## Documentation   
Please checkout the official docs: https://github.com/laradock/laradock

## laradock.sh Commands ##

    create                      Creates DB and docker compose.
    up                          Runs docker compose.
    down                        Stops containers.
    rebuild                     Rebuild containers.
    sync                        Manually triggers the synchronization of files.
    sync clean                  Removes all files from docker-sync.
    bash [--root]               Opens bash on the workspace, optionally as root user.
    wp [command]                Runs WP-CLI in one off container.
    composer [command]          Runs Composer in one off container.
    theme composer [command]    Runs Composer in theme directory.
    -- [command]                Executes any command in workspace.
    help [command]              Displays pptions.
    
Example:
    
    ./laradock.sh create
    ./laradock.sh composer install
    ./laradock.sh wp rewrite flush


## Roadmap

- [ ] `dnsmasq` instructions

- [ ] Setup script with user input to set variables

- [ ] Multi-Site Hosting

- [ ] Ansible Deployments
