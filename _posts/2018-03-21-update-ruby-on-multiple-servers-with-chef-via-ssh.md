---
layout: post
title: Update Ruby on multiple severs with Chef via SSH
tags: [Ruby, Chef]
---

## Clone the cookbook

Clone the cookbook from GitHub.

````
git clone https://github.com/RubyOps/update_ruby.git
````

## Install Chef on your computer

To install on Unix or Linux just run the following script.

````
curl -L https://omnitruck.chef.io/install.sh | sudo bash
````

For more options visit https://docs.chef.io/install_omnibus.html

### Test the `knife` environment

This command will test if knife properly loads it's configuration file from the cookbook. This command should be run inside the `update_ruby` directory.

````
cd update_ruby
knife environment list -V
````

### Install `knife-zero`

````
cd /opt/chef/embedded/bin/
sudo ./gem install knife-zero
````

`Knife-zero` will give you ability to deploy your cookbook via ssh with a command like the one below.

````
knife zero converge "name:server.myserver.com" --ssh-user chef --ssh-port 22
````

## Setup SSH login

Copy your public key to the remote server.

````
ssh-copy-id -i ~/.ssh/id_rsa.pub chef@server.myserver.com
````

This command will add your public key to `authorized_keys` which will enable key based login as the `chef` user.

#### Test SSH connectivity

````
ssh chef@server.myserver.com
````

## Sudo setup

Check if your user on the remote server belongs to the `sudo` group. In this case the user is named `chef`.

````
id chef
````

If not add the user to the `sudo` group.

````
usermod -G sudo chef
````
If you connect to your server as the `root` user then you can skip this step.

## Prepare the node

## Setup `Chef` on the server using `knife`

````
knife zero bootstrap chef@server.myserver.com --ssh-port 22 --sudo
````
This command installs chef on the remote server and creates it's json configuration file inside the nodes directory.

````
ls nodes/
````

Use `knife` to list the nodes.

````
knife node list
````

Show the node.

````
knife node show server.myserver.com
````

#### Add recipe to node's runlist

This is an important step since we will run tell chef what recipe to run on the cookbook.

````
knife node run_list add server.myserver.com 'recipe[update_ruby]'
````

Let's check if the recipe was added to the Run List.

````
knife node show server.myserver.com
````

# Execute the cookbok

This command will run the cookbok on the remote server.

````
knife zero converge "name:server.myserver.com" --ssh-user chef --ssh-port 22 --sudo
````
