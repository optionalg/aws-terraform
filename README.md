# AWS CoreOS cluster provisioning with [Terraform](http://www.terraform.io/downloads.html)

## Install Tools and Setup AWS credentials

1. Install [Terraform](http://www.terraform.io/downloads.html)

    For MacOS,
    ```
    $ brew update
    $ brew install terraform
    ```
    or
    ```
    $ mkdir -p ~/bin/terraform
    $ cd ~/bin/terraform
    $ curl -L -O https://dl.bintray.com/mitchellh/terraform/terraform_0.6.0_darwin_amd64.zip
    $ unzip terraform_0.6.0_darwin_amd64.zip
    ```

1. Install AWS CLI
    ```
    $ brew install awscli
    ```
    or

    ```
    $ sudo easy_install pip
    $ sudo pip install --upgrade awscli
    ```

1. Install [Jq](http://stedolan.github.io/jq/)
    ```
    $ brew install jq
    ```

1. Clone the repo    
    ```
    $ git clone git@github.com:xuwang/aws-terraform.git
    $ cd aws-terraform
    ```
1. Setup AWS Credentials at [AWS Console](https://console.aws.amazon.com/)
    1. Create a group `coreos-cluster` with `AaminstratorAccess` policy.
    2. Create a user `coreos-cluster` and download user credentials.
    3. Add user `coreos-cluster` to group `coreos-cluster`.

1. Configure AWS profile with `coreos-cluster` credentials
    ```
    $ aws configure --profile coreos-cluster
    ```

1. Setup AWS credintials for Terraform
    ```
    $ scripts/setup-aws-vars.sh
    ```
    This script will create coreos-cluster/tfcommon/keys.tfvars from AWS profile `coreos-cluster`.

1. Setup Shell Alisas
    ```
    $ source scripts/alias.sh
    ```
    This will create aliases for common terraform commands with options (key.tfvars, for instance). For example:
    ```
    alias tfp='tf plan --var-file=../tfcommon/keys.tfvars --var-file=../tfcommon/network.tfvars'
    alias tfa='tf apply --var-file=../tfcommon/keys.tfvars --var-file=../tfcommon/network.tfvars'
    ```

## Create VPC, Subnets, and Security Groups

    ```
    $ cd coreos-cluster/vpc
    ```

1. Review the `vpc-net.tf` and then:
    ```
    $ tfp
    $ tfa
    ```

1. Generate ../tfcommon/network.tfvars based on the output of the above steps. 
The network.tfvars will be used by other resouces:
    
    ```
    $ ./gen-network-tfvars.sh
    ```

1. To destroy VPC:
    ```
    $ tfpd
    $ tfda
    ```

## Create S3 Buckets 

    ```
    $ cd coreos-cluster/s3
    $ tfp
    $ tfa
    ```

## Create AMI Roles

## Create Other Cluster Components

Create other components, i.e. coreos-cluster/etcd coreos-cluster/dockerhub, coreos-cluster/worker, etc.

    ```
    $ cd coreos-cluster/<component>
    ```

1. Plan and apply desired component:
    ```
    $ tfp 
    ```
    If the plan looks good:
    ```
    $ tfa
    ```

1. Destroy component:
    ```
    $ tfpd
    $ tfda
    ```
