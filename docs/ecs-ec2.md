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