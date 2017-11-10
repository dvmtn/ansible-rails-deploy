# Ansible role to deploy rails applications

This role was developed to be an idempotent deployment for rails applications, and was heavily inspired by [nickjj.rails](https://github.com/nickjj/ansible-rails).

this role will always run migrations and seeds, even if they have not changed. We belive deployments should be idempotent, if your deployment halts part way through you want to know you can just run it again without any problems.

##Requirements

- If your git repositry is private you will need to set an SSH keypair, OAuth tokens or another way to pull your code. (We use SSH deploy keys)
- You will need to set up any enviorment variables this repo requires and put them in `/etc/default/{{ rails_deploy_app_name }}`, as this role will try to source that file.


## Role variables

Below is a list of default values along with a description of what they do.

```
# What is the name of your rails app? It should be lower case and a valid file name.
rails_deploy_app_name: app

# Which user account on the system will own the deployed files?
rails_deploy_user: deploy

# Where will the deployed application live on your server?
rails_deploy_path: /home/{{ rails_deploy_user }}/{{ rails_deploy_app_name }}

# The git url for your repo
rails_deploy_git_url: "git@bitbucket.org:yourusername/reponame.git"

# Which branch or tag should be used?
rails_deploy_git_version: master

# By default it expects you have ruby installed through rvm but this is not mandatory.
# Just replace this with the path to your ruby binary.
rails_deploy_shell_prefix: /usr/local/rvm/wrappers/default

# What command will start bundler? You shouldn't ever have to change this once you
# have setup the shell prefix above unless you don't use rvm.
rails_deploy_bundle: "{{ rails_deploy_shell_prefix }}/bundle"

# Where will the gems be installed to? This path is relative to your rails app by default
# but you can put an absolute path if you want.
rails_deploy_bundle_path: vendor/bundle

# A full list of possible environments that your app could run against. This is used by
# bundle --without and it will figure out which environments to remove for you.
rails_deploy_bundle_without:
  - development
  - staging
  - production
  - test

# Should it run migrations and precompile assets?
rails_deploy_migrate: true
rails_deploy_precompile_assets: true

# This is the single app server that will run any command that issues a db related task.
# It will by default look for a group called [app] and then pick the first server in the list
# but you can change it to whatever you want.
rails_deploy_migrate_master_host: "{{ groups['app'][0] }}"

# The amount in seconds to cache apt-update.
apt_cache_valid_time: 86400
```
