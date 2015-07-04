# -*- mode: ruby -*-
# vi: set ft=ruby :
#

# Config Github Settings
GITHUB_USERNAME = "fabriziogiordano"
GITHUB_REPO     = "Vaprobash"
GITHUB_BRANCH   = "master"
GITHUB_URL      = "https://raw.githubusercontent.com/#{GITHUB_USERNAME}/#{GITHUB_REPO}/#{GITHUB_BRANCH}"

# Because this:https://developer.github.com/changes/2014-12-08-removing-authorizations-token/
# https://github.com/settings/tokens
GITHUB_PAT = ""

# Project Settings
PROJECT_NAME = "es6"

# Server Configuration
SERVER_HOSTNAME = "es6.dev"

# Set a local private network IP address.
# See http://en.wikipedia.org/wiki/Private_network for explanation
# You can use the following IP ranges:
#   10.0.0.1    - 10.255.255.254
#   172.16.0.1  - 172.31.255.254
#   192.168.0.1 - 192.168.255.254
SERVER_IP = "192.168.22.10"
SERVER_CPUS = "1"   # Cores
SERVER_MEMORY = "384" # MB
SERVER_SWAP = "768" # Options: false | int (MB) - Guideline: Between one or two times the server_memory

# UTC        for Universal Coordinated Time
# EST        for Eastern Standard Time
# US/Central for American Central
# US/Eastern for American Eastern
SERVER_TIMEZONE  = "UTC"

# Languages and Packages

# Node Options
NODEJS_VERSION = "latest"   # By default "latest" will equal the latest stable version
NODEJS_PACKAGES = [          # List any global NodeJS packages that you want to install
  "grunt-cli",
  "babel",
  #"gulp",
  #"bower",
  #"yo",
]

# PHP Options
PHP_TIMEZONE = "UTC"    # http://php.net/manual/en/timezones.php
PHP_VERSION = "5.6"    # Options: 5.5 | 5.6

# To install HHVM instead of PHP, set this to "true"
HHVM = "false"


VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    # Set server to Ubuntu 14.04
    config.vm.box = "ubuntu/trusty64"
    config.vm.box_check_update = false

    # Prevent TTY Errors
    # config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

    ## VAGRANT-HOSTMANAGER ##
    # vagrant plugin install vagrant-hostmanager
    # https://github.com/smdahlen/vagrant-hostmanager
    if Vagrant.has_plugin?("vagrant-hostmanager")
        config.hostmanager.enabled = true
        config.hostmanager.manage_host = true
        config.hostmanager.ignore_private_ip = false
        config.hostmanager.include_offline = false
    end

    # Create a hostname, don't forget to put it to the `hosts` file
    # This will point to the server's default virtual host
    # TO DO: Make this work with virtualhost along-side xip.io URL
    config.vm.hostname = SERVER_HOSTNAME

    # Create a static IP
    config.vm.network :private_network, ip: SERVER_IP
    config.vm.network :forwarded_port, guest: 80, host: 8000


    # Use NFS for the shared folder
    # http://askubuntu.com/questions/412525/vagrant-up-and-annoying-nfs-password-asking
    ##config.vm.synced_folder ".", "/vagrant",
    ##          id: "core",
    ##          :nfs => true,
    ##          :mount_options => ['nolock,vers=3,udp,noatime']


    # If using VirtualBox
    config.vm.provider "virtualbox" do |vb|
        # Set box name
        vb.name = PROJECT_NAME

        # Set server cpus
        vb.customize ["modifyvm", :id, "--cpus", SERVER_CPUS]

        # Set server memory
        vb.customize ["modifyvm", :id, "--memory", SERVER_MEMORY]

        # Allow symbolic links
        vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]

        # Set the timesync threshold to 10 seconds, instead of the default 20 minutes.
        # If the clock gets more than 15 minutes out of sync (due to your laptop going
        # to sleep for instance, then some 3rd party services will reject requests.
        vb.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]

        # Prevent VMs running on Ubuntu to lose internet connection
        # vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        # vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end

    # If using Vagrant-Cachier
    # vagrant plugin install vagrant-cachier
    # http://fgrehm.viewdocs.io/vagrant-cachier
#    if Vagrant.has_plugin?("vagrant-cachier")
        # Configure cached packages to be shared between instances of the same base box.
        # Usage docs: http://fgrehm.viewdocs.io/vagrant-cachier/usage

        #config.cache.scope = :box

        #config.cache.synced_folder_opts = {
        #    type: :nfs,
        #    mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
        #}
#    end

    # If using Vagrant-Exec
    # https://github.com/p0deje/vagrant-exec
    # vagrant plugin install vagrant-exec
    # vagrant exec --binstubs
    #
    # Add DIRENV
    # http://direnv.net/
    # brew install direnv
    # echo PATH_add binstubs > .envrc
    # direnv allow .

#    if Vagrant.has_plugin?("vagrant-exec")
        config.exec.binstubs_path = 'binstubs'

        # Automatically prepend apt-get command with sudo:
        #   ➜ vagrant exec apt-get install htop
        #   # is the same as
        #   ➜ vagrant ssh -c "cd /vagrant && sudo apt-get install htop"
        config.exec.commands 'apt-get', prepend: 'sudo'

        config.exec.commands 'sudo'
        config.exec.commands 'node'
        config.exec.commands 'npm'
        config.exec.commands 'php'

        # brew install direnv
        # echo PATH_add binstubs > .envrc
        # direnv allow .
        # vagrant exec --binstubs
#    end

    # If using Vagrant-Triggers
    # https://github.com/emyl/vagrant-triggers
    # vagrant plugin install vagrant-triggers
    # if Vagrant.has_plugin?("vagrant-triggers")
    #     {
    #       [:up, :resume] => "brew install direnv && echo PATH_add binstubs > .envrc && direnv allow . && vagrant exec --binstubs"
    #     }.each do |command, trigger|
    #       config.trigger.before command, :stdout => true do
    #         info "Executing #{command} action on the VirtualBox tied VM..."
    #         run  trigger
    #       end
    #     end
    # end

    # Add a marker file to only provision once
    #config.vm.provision "shell", inline: $vagrant_provision_marker_start

    ####
    # Base Provisioning Items
    ##########

    # provision Base Packages
    config.vm.provision "shell", path: "#{GITHUB_URL}/scripts/base.sh", args: [GITHUB_URL, SERVER_SWAP, SERVER_TIMEZONE]

    # optimize base box
    config.vm.provision "shell", path: "#{GITHUB_URL}/scripts/base_box_optimizations.sh", privileged: true

    ####
    # Additional Languages
    ##########

    # Install Nodejs
    config.vm.provision "shell", path: "#{GITHUB_URL}/scripts/nodejs.sh", privileged: false, args: NODEJS_PACKAGES.unshift(NODEJS_VERSION, GITHUB_URL)

    # Provision PHP
    config.vm.provision "shell", path: "#{GITHUB_URL}/scripts/php.sh", args: [PHP_TIMEZONE, HHVM, PHP_VERSION]

    # All provisioned then add the marker file
    config.vm.provision "shell", inline: $vagrant_provision_marker_end


end



$vagrant_provision_marker_start = <<-CONTENTS

MARKER_FILE="/usr/local/etc/vagrant_provision_marker"
# Only provision once
if [ -f "${MARKER_FILE}" ]; then
  exit 0
fi
CONTENTS

$vagrant_provision_marker_end = <<-CONTENTS

MARKER_FILE="/usr/local/etc/vagrant_provision_marker"
# Touch the marker file so we don't do this again
touch ${MARKER_FILE}

CONTENTS
