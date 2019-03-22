---
layout: post
title: Chef wrapper cookbook to install and update Ruby
tags: [Ruby, Chef]
---

## Generate a wrapper cookbook

This tutorial will show you the steps

### Install ChefDK

Go to and download Chef Development Kit https://downloads.chef.io/chefdk/

## Install Chef on your computer

To install on Unix or Linux just run the following script.

````
curl -L https://omnitruck.chef.io/install.sh | sudo bash
````

For more options visit https://docs.chef.io/install_omnibus.html

## Create the project directory

````
mkdir update_ruby
````

### Create the `knife` configuration file

Inside the `update_ruby` cookbook create the knife config file.

````
cd update_ruby
mkdir .chef
echo 'local_mode true' >> .chef/knife.rb
echo 'chef_repo_path "./"' >> .chef/knife.rb
````

### Test the `knife` environment

This command will test if knife properly loads it's configuration file from the cookbook. This command should be run inside the `update_ruby` directory.

````
knife environment list -V
````

If you get an error make sure you created the `knife.rb`.

````
ls .chef/knife.rb
````

### Create the `cookbooks` directory

Since this is a wrapper cookbooks we will be using other cookbooks to do the work and therefore we need a directory where we should install them.

````
mkdir cookbooks
````

### Generate the cookbook

````
cd cookbooks/
chef generate cookbook update_ruby
````

## Install dependecies

For more info on the cookbook visit link below.

https://supermarket.chef.io/cookbooks/ruby_rbenv

Download the dependencies to cookbooks directory.

````
cd cookbooks
pwd
knife supermarket download ruby_rbenv
`````

Extract dependency inside cookbooks directory

````
tar -xzvf ruby_rbenv-*.tar.gz
````

Remove the archive.

````
rm ruby_rbenv-*.tar.gz
````

### Add dependencies to `update_ruby` cookbook

We will use the `ruby_rbenv` cookbook to install and manage Ruby versions.

Add this line to `cookbooks/update_ruby/metadata.rb`

````
depends 'ruby_rbenv'
````

## Add attributes to update_ruby cookbook

````
cd cookbooks/update_ruby/
````

Create file `attributes/default.rb`

````
mkdir attributes
touch attributes/default.rb
````

Add the default action and Ruby version to install to `attributes/default.rb`.

````
# RBENV
default['rbenv']['action'] = 'install' # install / uninstall
default['rbenv']['version'] = '2.6.2'
````

Open the recipes default file located at `recipes/default.rb` and add following.

{% highlight bash %}

version = node['rbenv']['version']

# Install Rbenv Globally
# Install Rbenv to the system path e.g. /usr/local/rbenv
rbenv_system_install 'system'

# Install a Ruby version
rbenv_ruby version do
  verbose true
  rbenv_action node['rbenv']['action']
end

# Set that Ruby as the global Ruby
rbenv_global version

rbenv_rehash 'rehash' do
end

rbenv_script 'gem update system' do
  rbenv_version version
  code "gem update --system"
end
{% endhighlight %}
