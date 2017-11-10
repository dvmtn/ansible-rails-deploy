# Ansible role to deploy rails applications

This role was developed to be an idempotent deployment for rails applications, and was heavily inspired by [nickjj.rails](https://github.com/nickjj/ansible-rails).

this role will always run migrations and seeds, even if they have not changed. We belive deployments should be idempotent, if your deployment halts part way through you want to know you can just run it again without any problems.

## Requirements

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

# All gem groups you do not want installed on your production servers
# it will be passed to bundle --without
rails_deploy_bundle_without:
  - development
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

## Example playbook

For the sake of this example let's assume you have a group called **app** and you have a typical `main.yml` file.

To use this role edit your `main.yml` file to look something like this:

```
---
- name: ensure app servers are configured
- hosts: app

  roles:
    # Insert other roles here to provision the server before deploying a rails app to it.
    - { role: dvmtn.rails-deploy, tags: rails }
```

Let's say you want to edit a few defaults, you can do this by opening or creating `group_vars/all.yml` which is located relative to your `inventory` directory and then making it look something like this:

```
---
# Variables that could have been populated to satisfy other roles, it doesn't matter.
user_name: dvmtn
secrets_load_path: /home/yourname/dev/secrets

# Overwrite a few rails deploy variables.
rails_deploy_app_name: testproj
rails_deploy_user: "{{ user_name }}"

rails_deploy_git_url: "git@bitbucket.org:yourname/your-project.git"

rails_deploy_migrate_master_host: "{{ groups['app'][0] }}"
```

#### Tracking the repo and migration status in other roles

 - This role sets `rails_repo_status_changed` which will track if the repo was modified or not
 - This role sets `rails_deploy_ran_migration` which will track if a migration was run

## Installation

`$ ansible-galaxy install dvmtn.rails-delpoy`


## Requirements

Tested on ubuntu 14.04 and 16.04 LTS but it should work on other versions that are similar.

## Ansible galaxy

You can find it on the official ansible galaxy [here](https://galaxy.ansible.com/dvmtn/rails-deploy/).

## License

MIT
