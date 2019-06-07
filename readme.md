## This repository is created with learning purposes for Terraform, focusing how to use KitchenCI alongside Terraform.

## Purpose :

- It provides a simple example of how to use KitchenCI to verify the provisioned with Terraform infrastructure.

## How to install terraform : 

- The information about installing terraform can be found on the HashiCorp website 
[here](https://learn.hashicorp.com/terraform/getting-started/install.html)

## How to setup KitchenCI and RBENV (MacOS Mojave 10.14.5) :

## Setup KitchenCI:

- For using [KitchenCI](https://kitchen.ci/), ruby environment needs to be set up first.
- In a directory of your choice, clone the github repository :
    ```
    git clone https://github.com/martinhristov90/terraformKitchen.git
    ```
- Change into the directory :
    ```
    cd terraformKitchen
    ```
- Run `brew install ruby`
- After previous command finish, run `gem install rbenv`, this would give you ability to choose particular version of Ruby. This is a prerequisite.
- Next, [Bundler](https://bundler.io) needs to be installed, run `gem install bundler`, this would provide the dependencies that KitchenCI needs. It is going to install the Gems defined in the `Gemfile`
- Run the following two commands, to setup Ruby environment for the local directory.
    ```bash
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    ```
- Reload your BASH interpreter or apply the changes to the profile :
    ```shell
    source ~/.bash_profile 
    ```
- Verify rbenv is installed properly with :
    ```shell
    type rbenv   # → "rbenv is a function"
    ```
- To install the particular version that we need, run the following command in the project directory:
    ```shell
    rbenv install 2.5.3
    ```
- Set local version to be used with command :
    ```shell
    rbenv local 2.5.3
    ```
- Previous step is going to create a file named .ruby-version, with the following content `2.5.3`
- Install the needed Gems for KitchenCI using Bundle with command :
    ```shell
    bundle install
    ```
- Now you should have KitchenCI installed and running, verify with `bundle exec kitchen list`, the output should look like :
    ```
    Instance        Driver     Provisioner  Verifier   Transport  Last Action    Last Error
    default-ubuntu  Terraform  Terraform    Terraform  Ssh        <Not Created>  <None>
    ```

## How to use it :

- Make sure that you are in the project's directory.

- Create a filed named `testing.tfvars` with the following content that fits for you:
    ```
    access_key = "YOUR_ACCESS_KEY"
    secret_key = "YOUR_SECRET_KEY"
    key_name = "terraformKitchen"
    region = "us-east-1"
    ami = "ami-fce3c696"
    instance_type = "t2.micro"
    ```
- Create a SSH key-pair in AWS, name it `terraformKitchen`, more information how to create it can be found [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

- Save the corresponding private key of the pair with name `terraformKitchen.pem` in the project's directory.

- Now, `bundle exec kitchen converge` should be used to provision the infrastructure that is going to be tasted, the output should look like :
    ```
       Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

       Outputs:

       public_dns = YOUR_INSTANCE_DNS_NAME_IS_GOING_TO_SHOW_UP_HERE.amazonaws.com
       Finished converging <default-ubuntu> (0m49.24s).
    -----> Kitchen is finished. (1m3.30s)
    ```
- Now, the testing of the AWS instance is going to be performed to verify that the AWS is `Ubuntu` indeed. Run `bundle exec kitchen verify` The output should look like :
    ```
    Profile: default
    Version: (not specified)
    Target:  ssh://YOUR_INSTANCE_DNS_NAME_IS_GOING_TO_SHOW_UP_HERE.amazonaws.com:22

      ✔  operating_system: Command: `lsb_release -a`
         ✔  Command: `lsb_release -a` stdout should match /Ubuntu/


    Profile Summary: 1 successful control, 0 control failures, 0 controls skipped
    Test Summary: 1 successful, 0 failures, 0 skipped
           Finished verifying <default-ubuntu> (0m16.96s).
    -----> Kitchen is finished. (0m20.69s)
    ````
- To destroy the `Kitchen` instance, execute `bundle exec kitchen destroy`


