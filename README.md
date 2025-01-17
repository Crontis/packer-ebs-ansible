# Dockerimage for packer

[Docker Hub](https://hub.docker.com/r/crontis/packer-ebs-ansible)

This image is used to run packer with an amazon-ebs builder and ansible-provisioner.
It also comes with the aws session manager plugin installed, to utilize packers session_manager connector.

An example usage for the session manager would be to prevent disconnects when changing the servers ip.

## Usage

Please note, that amazons ssm agent has to be preinstalled on your source ami!

    docker run -it \
        --mount type=bind,source=$(pwd),target=/build \
        crontis/packer-ebs-ansible:latest "packer build /build/template.json"

Please note, that you need to provide your aws credentials somehow. You can set them via your CI or provide them directly to docker:

    docker run -it \
        --mount type=bind,source=$(pwd),target=/build \
        -e AWS_ACCESS_KEY_ID="KEY" \
        -e AWS_SECRET_ACCESS_KEY="SECRET" \
        crontis/packer-ebs-ansible:latest "packer build /build/template.json"

## Packer example usage with session_manager
The ami has to be replace with an ami that has the ssm agent installed.

    {
        "variables": {
            "aws_access_key": "",
            "aws_secret_key": ""
        },
        "builders": [
            {
                "type": "amazon-ebs",
                "region": "eu-central-1",
                "access_key": "{{user `aws_access_key`}}",
                "secret_key": "{{user `aws_secret_key`}}",
                "associate_public_ip_address": true,
                "source_ami": "ami_with_ssm_agent",
                "instance_type": "t2.micro",
                "ssh_interface": "session_manager",
                "ssh_username": "centos",
                "iam_instance_profile": "SSMInstanceProfile",
                "ami_name": "centos {{timestamp}}"
            }
        ],
        "provisioners": [
            {
                "type": "ansible",
                "playbook_file": "your_playbook.yml"
            }
        ]
    }
