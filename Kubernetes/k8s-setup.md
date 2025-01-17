
#### Setup Kubernetes (K8s) Cluster on AWS


1. Create Ubuntu EC2 instance
1. install AWSCLI
   ```sh 
    curl https://s3.amazonaws.com/aws-cli/awscli-bundle.zip -o awscli-bundle.zip
    apt install unzip python2.7
    unzip awscli-bundle.zip
    #sudo apt-get install unzip - if you dont have unzip in your system
    ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
    ```
    
1. Install kubectl
   ```sh
   curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    chmod +x ./kubectl
    sudo mv ./kubectl /usr/local/bin/kubectl
   ```
1. Create an IAM user/role  with Route53, EC2, IAM and S3 full access
1. Attach IAM role to ubuntu server

    #### Note: If you create IAM user with programmatic access then provide Access keys. 
   ```sh 
     aws configure
    ```
1. Install kops on ubuntu instance:    follow-  https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
   ```sh
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

---------------------------
or
--------------------------------------------------------------------------------
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

./aws/install -i /usr/local/aws-cli -b /usr/local/bin

sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update

which aws

ls -l /usr/local/bin/aws

aws --version

----------------------------------------------------------------
    ```
1. Create a Route53 private hosted zone (you can create Public hosted zone if you have a domain)
1. create an S3 bucket 
   ```sh
    aws s3 mb s3://dev.k8s.prasanths.xyz
   ```
1. Expose environment variable:
   ```sh 
    export KOPS_STATE_STORE=s3://dev.k8s.prasanths.xyz
   ```
1. Create sshkeys before creating cluster
   ```sh
    ssh-keygen
   ```
1. Create kubernetes cluster definitions on S3 bucket 
   ```sh 
    
    ```kops create cluster --cloud=aws --zones=ap-south-1b --name=dev.k8s.prasanths.xyz --dns=prasanths.xyz --dns private
1. Create kubernetes cluser
    ```sh 
      kops update cluster dev.k8s.prasanths.xyz --yes
     ```
1. Validate your cluster 
     ```sh 
      kops validate cluster
    ```

1. To list nodes
   ```sh 
     kubectl get nodes 
   ```

#### Deploying Nginx container on Kubernetes 
1. Deploying Nginx Container
    ```sh 
      kubectl run sample-nginx --image=nginx --replicas=2 --port=80
      kubectl get pods
      kubectl get deployments
   ```
   
1. Expose the deployment as service. This will create an ELB in front of those 2 containers and allow us to publicly access them:
   ```sh 
    kubectl expose deployment sample-nginx --port=80 --type=LoadBalancer
    kubectl get services -o wide
    ```
 1. To delete cluster
    ```sh
     kops delete cluster dev.k8s.prasanths.xyz --yes
    ```
