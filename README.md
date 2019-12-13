
# Setting up AWS EKS (Hosted Kubernetes)
PART I: This is the first part of the 2 part installation.  The second part will come after cluster provisioning and dependency tooling is installed and ready to go.  At that point we will install Helm and Tiller, the package management system for Kubernetes clusters and then use it to create a "release" of Swimlane.  Eventually we install the Swimlane chart that we configure for production; at which point we will be live. 

## Kubectl

If you are on a mac, simply use homebrew.

If you configure a bastion host beforehand, choose a red-hat or fedora flavor distro.

$ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

$ chmod +x kubectl

$ sudo mv kubectl /usr/local/bin


## Download the aws-iam-authenticator

$ wget https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.3.0/heptio-authenticator-aws_0.3.0_linux_amd64

$ chmod +x heptio-authenticator-aws_0.3.0_linux_amd64

$ sudo mv heptio-authenticator-aws_0.3.0_linux_amd64 /usr/local/bin/heptio-authenticator-aws


## Modify providers.tf

Make changes in <providers.tf> if you would like to change where the control plane / master nodes are ran.
Alternatively, IF you define a credentials file at:

~/.ssh/credentials

you may specify the default region in addition to preferred output format to return requests in (json, yaml, xml, text)

## Planning, then Executing tne Provisioning of Cloud Resources

**$ terraform init** 

This is useful to forecast exactly what will be added/removed/changed in the target AWS account before any real changes are made.

$ terraform apply

This an take some time to run, so be prepared for that.

## Configure kubectl

$ mkdir ~/.kube/config

$ terraform output kubeconfig > ~/.kube/config

$ aws eks --region us-west-2 update-kubeconfig --name sg-sl-eks-cluster


## Configure config-map-auth-aws

$ terraform output config-map-aws-auth > config-map-aws-auth.yaml

$ kubectl apply -f config-map-aws-auth.yaml


## See nodes coming up

$ kubectl get nodes


## Destroy
Make sure all the resources created by Kubernetes are removed (LoadBalancers, Security groups), and issue:

$ terraform destroy

