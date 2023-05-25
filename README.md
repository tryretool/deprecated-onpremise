# Deprecated methods for deploying Retool on-premise

This repo contains deprecated methods for deploying Retool.

- [AWS one-click deploy](#one-click-deploy-to-aws)
- [ECS + EC2](#deploying-on-ecs-+-ec2)
- [Heroku](#deploying-retool-on-heroku)



### One-click Deployment to AWS

Region name | Region code | Launch
--- | --- | ---
US East (N. Virginia) | us-east-1 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3-external-1.amazonaws.com/cf-templates-x1ljyg3aygh-us-east-1/2021157Dqr-SSOPLaunchJuneo3g1bsca3hh&stackName=retool) 
US West (N. California) |	us-west-1 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://us-west-1.console.aws.amazon.com/cloudformation/home?region=us-west-1#/stacks/quickcreate?templateURL=https://s3-external-1.amazonaws.com/cf-templates-x1ljyg3aygh-us-east-1/2021157Dqr-SSOPLaunchJuneo3g1bsca3hh&stackName=retool) 
EU (Ireland) |	eu-west-1 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://eu-west-1.console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/quickcreate?templateURL=https://s3-external-1.amazonaws.com/cf-templates-x1ljyg3aygh-us-east-1/2021157Dqr-SSOPLaunchJuneo3g1bsca3hh&stackName=retool) 
Asia Pacific (Mumbai) |	ap-south-1 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://ap-south-1.console.aws.amazon.com/cloudformation/home?region=ap-south-1#/stacks/quickcreate?templateURL=https://s3-external-1.amazonaws.com/cf-templates-x1ljyg3aygh-us-east-1/2021157Dqr-SSOPLaunchJuneo3g1bsca3hh&stackName=retool)
US East (Ohio) | us-east-2 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/quickcreate?templateURL=https://s3-external-1.amazonaws.com/cf-templates-x1ljyg3aygh-us-east-1/2021157Dqr-SSOPLaunchJuneo3g1bsca3hh&stackName=retool) 
US West (Oregon) |	us-west-2 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/quickcreate?templateURL=https://s3-external-1.amazonaws.com/cf-templates-x1ljyg3aygh-us-east-1/2021157Dqr-SSOPLaunchJuneo3g1bsca3hh&stackName=retool) 
EU (Frankfurt) |	eu-central-1 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/quickcreate?templateURL=https://s3-external-1.amazonaws.com/cf-templates-x1ljyg3aygh-us-east-1/2021157Dqr-SSOPLaunchJuneo3g1bsca3hh&stackName=retool) 

### Deploying on ECS + EC2

We provide a [template file](/cloudformation/retool.yaml) for you to get started deploying on ECS.

1. In the ECS Dashboard, click **Create Cluster**
1. Select `EC2 Linux + Networking` as the cluster template.
1. In your instance configuration, enter the following:
   - Select **On-demand instance**
   - Select **t2.medium** as the instance type (or your desired instance size)
   - Choose how many instances you want to spin up
   - (Optional) Add key pair
   - Choose your existing VPC (or create a new one)
   - (Optional) Add tags
   - Enable CloudWatch container insights
1. Select the VPC in which you’d like to launch the ECS cluster; make sure that you select a [public subnet](https://stackoverflow.com/questions/48830793/aws-vpc-identify-private-and-public-subnet).
1. Download the [retool.yaml](/cloudformation/retool.yaml) file, and add your license key and other relevant variables.
1. Go to the AWS Cloudformation dashboard, and click **Create Stack with new resources → Upload a template file**. Upload your edited `retool.yaml` file.
1. Then, enter the following parameters:
   - Cluster: the name of the ECS cluster you created earlier
   - DesiredCount: 2
   - Environment: staging
   - Force: false
   - Image: `tryretool/backend:X.Y.Z` (But replace `X.Y.Z` with your desired version. See [Select a Retool version number](#select-a-retool-version-number) to help you choose a version.)
   - MaximumPercent: 250
   - MinimumPercent: 50
   - SubnetId: Select 2 subnets in your VPC - make sure these subnets are public (have an internet gateway in their route table)
   - VPC ID: select the VPC you want to use
1. Click through to create the stack; this could take up to 15 minutes; you can monitor the progress of the stack being created in the `Events` tab in Cloudformation
1. After everything is complete, you should see all the resources with a `CREATE_COMPLETE` status.
1. In the **Outputs** section within the CloudFormation dashboard, you should be able to find the ALB DNS URL. This is where Retool should be running.
1. The backend tries to guess your domain to create invite links, but with a load balancer in front of Retool you'll need to set the `BASE_DOMAIN` environment variable to your fully qualified domain (i.e. `https://retool.company.com`). Docs [here](https://docs.retool.com/docs/environment-variables).

#### OOM issues

If running into OOM issues (especially on larger instance sizes with >4 vCPUs)

- Verify the issue by going into the ECS console and checking the Service Metrics. Ideally
  - Memory utilization should fall around 40% (20% - 60%)
  - CPU utilization should be close to zero (0% - 5%)
- If the values fall outside these ranges, increase the CPU and memory allocation in `retool.yml`

### Deploying Retool on Heroku

#### Automatic deploy

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/tryretool/retool-onpremise)

You can deploy to Heroku using the following steps:

1. Click the button above to begin your deployment
1. Provide a unique App name, e.g. `<your-org>-retool`
1. Provide required config vars:
    * `LICENSE_KEY` - your Retool license key
1. Set any optional config vars:
    * `USE_GCM_ENCRYPTION` set to `true` for authenticated encryption of
      secrets; if true, `ENCRYPTION_KEY` must be 24 bytes
1. Click "Deploy app"


#### Manual deploy

You can manually deploy to Heroku using the following steps:

1. Install the Heroku CLI, and login. Documentation for this can be found here: <https://devcenter.heroku.com/articles/getting-started-with-nodejs#set-up>
1. Clone this repo `git clone https://github.com/tryretool/retool-onpremise`
1. Change the working directory to the newly cloned repository `cd ./retool-onpremise`
1. Create a new Heroku app with the stack set to `container` with `heroku create your-app-name --stack=container`
1. Add a database: `heroku addons:create heroku-postgresql:mini`
1. In the `Settings` page of your Heroku app, add the following environment variables:
   1. `NODE_ENV` - set to `production`
   1. `JWT_SECRET` - set to a long secure random string used to sign JSON Web Tokens
   1. `ENCRYPTION_KEY` - a long secure random string used to encrypt database credentials
   1. `USE_GCM_ENCRYPTION` set to `true` for authenticated encryption of secrets; if true, `ENCRYPTION_KEY` must be 24 bytes
   1. `LICENSE_KEY` - your Retool license key
   1. `PGSSLMODE` - set to `require`
1. _Optional_: To select the version of Retool used, just edit the first line
   under `./heroku/Dockerfile` to:
   ```docker
   FROM tryretool/backend:X.Y.Z
   ```
1. Push the code: `git push heroku master`

### Update Heroku deployment

To update a Heroku deployment that was created with the button above, you'll
need to push the desired changes to the Heroku remote.

```zsh
# This assumes you already have the `heroku` CLI installed and are logged in
heroku git:clone --app=<your-retool-app-name> # default remote name is "heroku"
git remote add upstream https://github.com/tryretool/retool-onpremise
# This will incpororate the remote changes into your _local_ copy
# Also assumes both branches are "master"
git pull upstream master
git push heroku master
```


