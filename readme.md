<!--ts-->
* [cka](#cka)
* [install local tools](#install-local-tools)
   * [Ubuntu](#ubuntu)
* [Set your local variables](#set-your-local-variables)
   * [export env variables](#export-env-variables)
* [Commands](#commands)
   * [list inventory](#list-inventory)
   * [ssh 2 EC2 instance](#ssh-2-ec2-instance)
      * [by k8s_role](#by-k8s_role)
   * [Update the readme.md TOC](#update-the-readmemd-toc)
* [Notes](#notes)
   * [ssm note](#ssm-note)
      * [useful ssm commands](#useful-ssm-commands)

<!-- Added by: axl, at: Wed Mar 16 14:25:58 PDT 2022 -->

<!--te-->
# cka
cka demo environment on public clouds
# install local tools
## Ubuntu
    cd ~
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    sudo apt-get install unzip
    unzip awscliv2.zip
    sudo ./aws/install
    sudo apt update
    sudo apt install python3-pip
    pip install boto
    pip install boto3
    pip install botocore
    pip install ansible
    sudo wget https://raw.githubusercontent.com/ekalinin/github-markdown-toc/master/gh-md-toc -O /usr/local/bin/gh-md-toc
    sudo chmod a+x /usr/local/bin/gh-md-toc

# Set your local variables
## export env variables
    export AWS_ACCESS_KEY_ID=""
    export AWS_SECRET_ACCESS_KEY=""
    export AWS_SESSION_TOKEN=""  ##If you have one
    export AWS_DEFAULT_REGION="us-west-2"
    export no_proxy=*  # For Macs experiencing "ERROR! A worker was found in a dead state" when running Ansible playbooks
# Commands
## list inventory
    ansible-inventory --list | jq '.'
## Stand up a full k8s stack
    ansible-playbook extra_plays/aws_k8s_deploy.yml
## ssh to EC2 instance
### by k8s_role
    ssh -l ubuntu $(ansible-inventory --host $(ansible-inventory --list | jq '._k8s_control.hosts[0]' -r) | jq '.network_interfaces[0].association.public_ip' -r)
    
## Update the readme.md TOC
    gh-md-toc --insert readme.md ; rm ./readme.md.toc.*; rm ./readme.md.orig.*

# Notes

## ssm note
If changing ansible connection to ssm you will need this package installed
    
    curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" -o "session-manager-plugin.deb"
    sudo dpkg -i session-manager-plugin.deb
### useful ssm commands

    /usr/local/bin/aws ssm start-session --target 'i-'
    /usr/local/bin/aws ssm start-session --target i-xxxxxxxxxxxxx --document-name AWS-StartPortForwardingSession --parameters '{"portNumber":["22"],"localPortNumber":["8022"]}'