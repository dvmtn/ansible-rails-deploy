** The following is a walkthrough of standing up a new Env with this project and may be useful in your applications mean README file **


### Initial deployment
1. Add you SSH public key to `./ansible/ssh_public_keys`
2. Add your account name and hashed password in `./ansible/group_vars/all.yml`
3. Add the new environment details into your `~/.ssh/config` file. Note that you may need to use the default user name and password/public key for your hosting platform.
4. Bootstrap the box with user roles via `ansible-playbook main.yml -i inventories/<target env> --tags=ansible_setup, users`. This command will fail at removing the default user as you are logged in with that account.
5. Update your `~/.ssh/config` file with your personal username and key path
6. Run the full deployment as yourself! `ansible-playbook main.yml -i inventories/<target env>` The failed removal of the default user will now succeed as you are using your own account.

### Regular deployments
Once the initial deployment is sorted, you can deploy just the rails app and associated environment variable files with the `rails` tag; for example: `ansible-playbook main.yml -i inventories/<target env> --tags=rails`

The branch to deploy defaults to `master` and can be changed by adding the extra argument `--rails_deploy_git_version=<BRANCH NAME>`

