## Continuous Deployments using Spinnaker on AWS and Kubernetes
--------------------------------------------------------------------------------------------------------------------------------------------
     1. Github Repository: https://github.com/in4it/spinnaker-course
        Docs: https://github.com/in4it/spinnaker-course/tree/master/docs
        Facebook group: https://www.facebook.com/groups/840062592768074/
     2. What is spinnaker
        Opensource multi-cloud continous delivery platform
        Initially developed by Netflix
        Build for releasing software changes with high velocity and confidence
        Designed with pluggability in mind
        Support for all major cloud Providers
           -Openstack, Google Cloud Platform, Google App Engine, Azure, AWs
        Replacement for Netflix Asgard, Open-sourced in 2015
     3. Why Spinnaker
        Reliable deployments, easy rollbacks, high velocity deployment, Flexible pipleline management system, Big active community, Integrations with major cloud prividers, Feature rich, CLI for setup and Admin, VM Bakery, RBAC, White-listed Execution Windows, Monitoring Integrations, Deployment Strategies, Notifications, Manual Judgements, Chaos Monkey Integration
         Spinnaker complements kubernetes by using powerful deployment pipelines allowing for multiple deployment strategies.
         Direct access to Spinnaker API, without being forced to use the UI. Deployments are buily-in and no custom scripting is needed.
     4. Halyard tool to configure, update and install Spinnaker
        Minio is S3 compatible storage
        Halyard Supported Storage:
          Azure Storage
          Google Cloud Storage
          Redis
          AWS S3
          Minio
     5. Installing Spinnaker (On cloud Digital Ocean)
           1-create-user.sh - Create user
           2-swpon.sh - Enable Swap ( for machines under 8GB of ram)
           3-install-halyard.sh - Installs Halyard
           4-configure-oauth.sh - Configures authentication on Spinnaker using Github(editing required)
           Go to Github->Settings->Developer settings 
           Register a new OAuth application(Application Name, Hpmepage Url (Ip address of machine created in cloud), Authorization callback URL )
           On Registring you will get Client ID and Client Secret . Use in script 4.
           5-deploy-spinnaker.sh - Deploys spinnaker with halyard and enables authentication using Github
           6-restart-spinnaker.sh - Makes sure everything is cleanly started and the latest configuration files are reloaded.
     6. Installing Locally
            vagrant(Hashicorp) - Open-source software product for building portable machines
            Virtualbox(Oracle) - Free cross-platform virtualization application (like vmware)
            Go to https://github.com/in4it/spinnaker-course/vagrant
            vagrant up ( use Vagrantfile and install-spinnaker.sh)
     7. Spinnaker Concepts
            Machines will be grouped into clusters
            Releases will be managed by deployments
        Spinnaker has 2 core sets of features to achieve this:
	    Cluster Management :  "manage your cluster"
            Deployment Management: "manager your deployments"
        Cluster Management - View and manage your cluster resources in the cloud
        A cluster can be both on infrastructure level (Virtual Machines in the cloud) or orchestrator (Kubernetes)/ PaaS level (Google App Engine)
        Deployment Management - Create and manage continous delivery workflows
        You are going to use workflows to descrive the way applications should be deployed on your cluster. These workflows you'll create are called the delivery pipeline in Spinnaker.
     8. Terminology
        Account: contain an Account Name and a credential that is able to authenticate against
        e.g AWS (AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY)
        or in Kubernetes: Credentials to the docker registries where your images are stored and Credentials to access the Kubernetes api-server.
        Cluster:
            Instance - VM instance on cloud or Kubernetes Pod
            Server Group- A Collection of instances , running the deployed software
            Spinnaker Deployment - Logically grouping of server groups. Sinnaler uses its own Orchestration and can be diffrenet from built in mechanisms.
       Providers:  A set of resources that spinnaker has control over.
       You drploy applications on server groups which are tied to a Cloud Provider.
       To get access to a provider, you need to configure an "Account"
       You can have multiple accounts per Provides ( e.g dev/Stagging/Prod)
       Accounts keep the credentials for your provider
       Spinnaker will use the Accounts to authenticate against a Cloud Provider.     9. Supported providers
        Google App Engine, Openstack, AWS, Azure, DC/OS, Google Compute Engine, Kubernetes(legacy) and kubernetes v2(manifest based). Legacy uses its own orchestration, v2 maps nicer to Kubernetes ( and also doesn't need docker registry credentials )
     10. Deployment Strategies - Determine how the new application version will be rolled out, minimizing/avoding impact to the application or customers.
         Spinnaker supports following deployment strategies Red/Black, Rolling red/black, canary. 
      11. Deployment Pipelines : Multiple pipeline stages form the spinnaker pipeline. Parameters can be passed between stages. Pipelines can be triggered Muannly or automatically.
      12. AWS : VPC, EC2 IAM Role, EC2 key pair, AMI, S3, Access Keys (Access Key ID, Secret access key )
      13. Managing and Managed Account : Managing - The spinnaker accoungt used to login to AWS, Managwed - The spinnaker account where spinnaker actually is able to modify resources in. Authentication from the managing to the managed account is then handled via IAM roles in the managed and managing account and not in spinnaker. The reason for this spilt is that you only have to maintain your cresdentials to the AWS once instead of adding them to every AWS account in your environment.
      14.Install AWS CLI , AWS Configure, aws s3 sync . S3://spinnaker-debian-repo-join to sync your debian artifact to S3 buckect, Security groups (Privta TCP 3000, Public ALB ) , Copy rosco.yml which is bakery to create images using packer you can create images for CGP, AWS and Azure, add debian registry to rosco.yml set base image using AMI id in AWS. 
      15. Create application ,create ALB assign SG,new pipeline add stages bake, deploy.
      16 Spinnaker with Jenkins : Update SpinnakerFirewall, hal config ci jenkins enable , hal config ci jenkins master add my-jenkins-master --address http://167.99.194.152:8080 --username sam -password , csrf: true ( to enable cross site projection) in hal file , 5-deploy-spinnaker.sh to deploy , 6-restart-spinnaker.sh , publish DEB file, configure jenkins, Configure spinnaker 
      17: Baking Custom AMIs : using Packer plugin and a provisioner in packer ,          include the manifest.json in spinnaker jenkins configuration propertyfile field.
     18: Building Pipelines : A pipeline exceutes on or more stages with a specific stage type. Every stage except the first one, has a dependency on one of the previous stages. You can run stages in parallel if you want ( by defining the same dependency). YOu can declare more than 1 dependenct per stage. You typically edit the stages in the web UI, but behind the scnens a JSON is generated. You can also choose to manually edit the JSON. Stages a) Bake - Bakes an image . uses Rosco( Spinnaker) and packer(hashicorp) b) Check Preconditions - Checks for preconditions before continuing, c) Clone server Group - Makes a copy of a server group. d) Deploy - Creates a new server group based on the previously baked or found image. e) Destroy Server Group - Destroys a previous/newewst/oldest server group f) Disable Cluster - Disables the cluster. e) Disable Server Group - Disbales newest/previous/oldest server group f) Enable Server Group - Enables newest/previous/oldest server group g) Find Image from Cluster - Selects an image from a server group within a cluster. If multiple server groups exist, takes largest/newest/oldest or fail. h ) Find Image from tags - Finds an image based on key/value filet of the tag i) Jenkins - Runs jenkins Job j) Manual judgement - Ask user input. h) Modify scaling process - Modiles Scaling parameters i ) Pipeline - Runs a pipeline j)  Resize Server Group - Resize a server group k ) Rollback Cluster - Rolls back Cluster i ) Scale Down/Shrink Cluster - removes server groups from a cluster j ) Script - Runs a custom python/groovy/shell script k) Tag Image - Tags image with key valye pair l) Wait - Wait a specified time m) Webhook - Invokes a webhook based on URL using a JSON payload.
     19: Notification : emailtrap free service for testing.
     20: Kayenta: Automated Canary Analysis from Google and netflix 
     21: Deploy to Kubernetes : Kubernetes v1 vs v2 provider
         Kubernetes V1 provider : Uses Spinnaker opionated way of deploying for Kubernetes. Uses Services and ReplicaSet/Deployment objects to deploy your application.
         Kubernetes V2 provider: Uses "manifest" files which you have to supply to Spinnaker. You decided yourself how to deploy on Kubernetes.
