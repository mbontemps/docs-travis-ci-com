---
title: AWS OpsWorks Deployment
layout: en
permalink: opsworks/
---

Travis CI can automatically deploy your [AWS OpsWorks](https://aws.amazon.com/en/opsworks/) application after a successful build.

For a minimal configuration, all you need to do is add the following to your `.travis.yml`:

    deploy:
      provider: opsworks
      access-key-id: ACCESS-KEY-ID
      secret-access-key: SECRET-ACCESS-KEY
      app-id: APP-ID     

You can obtain your AWS Access Key Id and your AWS Secret Access Key from [here](https://console.aws.amazon.com/iam/home?#security_credential). It is recommended to encrypt your AWS Secret Access Key. Assuming you have the `travis` client installed, you can do it like this:
    
    $ travis encrypt SECRET-ACCESS-KEY --add deploy.api_key

You can also have the `travis` tool set up everything for you:

    $ travis setup opsworks

Keep in mind that the above command has to run in your project directory, so it can modify the `.travis.yml` for you.

### Migrate the Database

If you want to migrate your rails database on travis to AWS OpsWorks, add the `migrate` option to your `.travis.yml`.

    deploy:
      provider: opsworks
      access-key-id: ACCESS-KEY-ID
      secret-access-key: SECRET-ACCESS-KEY
      app-id: APP-ID
      migrate: true    

### Branch to deploy from

By default, Travis CI will only deploy from your **master** branch.

You can explicitly specify the branch to deploy from with the **on** option:

    deploy:
      provider: opsworks
      access-key-id: ACCESS-KEY-ID
      secret-access-key: SECRET-ACCESS-KEY
      app-id: APP-ID     
      on: production

Alternatively, you can also configure it to deploy from all branches:

    deploy:
      provider: opsworks
      access-key-id: ACCESS-KEY-ID
      secret-access-key: SECRET-ACCESS-KEY
      app-id: APP-ID     
      on:
        all_branches: true

Builds triggered from Pull Requests will never trigger a deploy.

### Deploying build artifacts

After your tests run and before the deploy stage, Travis CI will clean up any additional files and changes you made.

Maybe that is not what you want, as you might generate some artifacts (think asset compilation) that are supposed to be deployed, too. There is now an option to skip the clean up:

    deploy:
      provider: opsworks
      access-key-id: ACCESS-KEY-ID
      secret-access-key: SECRET-ACCESS-KEY
      app-id: APP-ID     
      skip_cleanup: true

### Conditional Deploys

It is possible to make deployments conditional using the **on** option:

    deploy:
      provider: opsworks
      access-key-id: ACCESS-KEY-ID
      secret-access-key: SECRET-ACCESS-KEY
      app-id: APP-ID     
      on:
        branch: staging
        node: 0.11

The above configuration will trigger a deploy if the staging branch is passing on NodeJS 0.11.

Available conditions are:

* **all_branches** - when set to true, trigger deploy from any branch if passing
* **branch** - branch or list of branches to deploy from if passing
* **tags** - when set to true, Travis CI only deploys on tagged builds
* **condition** - custom condition or list of custom conditions
* **jdk** - JDK version to deploy from if passing
* **node** - NodeJS version to deploy from if passing
* **perl** - Perl version to deploy from if passing
* **php** - PHP version to deploy from if passing
* **python** - Python version to deploy from if passing
* **ruby** - Ruby version to deploy from if passing
* **repo** - only trigger a build for the given repository, to play nice with forks

### Running commands before and after deploy

Sometimes you want to run commands before or after deploying. You can use the `before_deploy` and `after_deploy` stages for this. These will only be triggered if Travis CI is actually deploying.

    before_deploy: "echo 'ready?'"
    deploy:
      ..
    after_deploy:
      - ./after_deploy_1.sh
      - ./after_deploy_2.sh
