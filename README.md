# Geni Vidi Vici

**Tool/Services**

1.  Docker:
    Docker has many advantages but the most important one is the container analogy. 
    That you don't only ship your code but ship the whole OS and all related layers with each deployment in a standard way. 
    It is a complete package containing the right version of OS, the desired version of the language, any external dependencies of your application and your application code. 
    That too in a lightweight, isolated and resource controllable way, and can run multiple copies of a single application.
2.  Kubernetes (AWS EKS):
    System for deploying, scaling, and managing containerized applications, 
    Kubernetes handles the work of scheduling containers onto a compute cluster and manages the workloads to ensure they run as the user intended.
    Kubernetes has other built-in features like self-healing and automated rollouts/rollbacks, effectively managing the containers for you.
3.  EKS:
    Amazon’s Elastic Kubernetes Service (EKS) is a managed Containers-as-a-Service 
    offering that significantly simplifies the deployment of Kubernetes on AWS. 
    With EKS, you simply create your own Kubernetes workers through the EKS Wizard. 
    Creating the Kubernetes master cluster and configuring networking, service discovery and other Kubernetes primitives is done for you.
4.  eksctl:
    eksctl is a tool jointly developed by AWS and Weaveworks that automates 
    much of the experience of creating EKS clusters.
    It will create two CloudFormation stack for cluster itself and 1 nodegroup stack(s).
    include: new vpc, internetGateway, NATGateway, AutoScaling Group, Rout Tables, 
    Security Groups, IAM Roles, 3 private subnents and 3 publice across different avaliblity zones, EIP'S...
5.  AWS ELB: 
    Amazon AWS Elasic Load Balancer is a purpose built service for distributing workloads.
    ELB scales your load balancer as traffic to your application changes over time. 
    ELB can scale to the vast majority of workloads automatically, 
    and increases the availability and fault tolerance of an application.
6.  GitLab(CI/CD and K8s integration):
    GitLab has repositories and version control, but also you can use GitLab CI/CD to build and deploy your app automatically.
    The k8s cluster is integrated with GitLab, special deployment variables are made available to your job and configuration is not required.     
    

**Prerequisites**

*  Let’s begin with setting up a Kubernetes cluster.
    I choosed using the eksctl utility which makes it really easy to set up an AWS EKS Cluster and a worker node group.

*  Check out the documentation on eksctl.io to install and configure the tool.
    https://eksctl.io/
    https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

*  Then you can spin up the Kubernetes cluster with the following command:
    *eksctl create cluster --name=myheritage-technical-exam --nodes=1 --node-type t3.small*

*  This will take around 10 to 15 minutes. Once the cluster is created, 
    you can set up your kubeconfig file using the AWS CLI’s update-kubeconfig command as follows:
    *aws eks update-kubeconfig --name myheritage-technical-exam*

*  Check to see if your worker node has properly registered with the following command:
    *kubectl get nodes*

*  Finally we’ll create a gitlab service account that we’ll use to deploy to Kubernetes from GitLab.
    There is a file called gitlab-service-account.yaml in the repository,
    This will create a new service account and attach admin permissions to it.
    Apply these settings with the following command:
    *kubectl apply -f gitlab-service-account.yaml*

*  Next up we’re going to set up a Docker registry to which we can push the application that we’ll dockerize.

*    As we’re going to connect to both the Docker Hub and to Kubernetes, 
     we need to specify some authentication configuration. 
    When you’re in your repository, use the left menu to open up the Settings -> CI / CD -> Environment variables. 
    We’re going to add the following configuration:

*DOCKER_USER* - This is the Docker user you use to login to the Docker Hub.

*DOCKER_PASSWORD* -  This is the Docker passwrod you use to login to the Docker Hub.

*CERTIFICATE_AUTHORITY_DATA* - This is the CA configuration for the Kubernetes cluster. For EKS, login to the AWS EKS console and open up your cluster configuration. You can find the Certificate Authority on the right.

*SERVER* - This is the endpoint to the Kubernetes API for our cluster. You can find this on the page where you already are.

*USER_TOKEN* - This is the token for the user that we’ll use to connect to the Kubernetes cluster. We need to find the token for the user that we created earlier. First, list all secrets with kubectl get secrets. There will be a secret starting with gitlab-service-account-token-, which is the token for the GitLab user we created earlier. Copy the NAME for this secret, and run the following command to see the token: kubectl describe secret [NAME]. Copy the token that is part of the output, and enter it in GitLab.






