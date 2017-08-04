# cdriehuys.ami-builder


### Overview

This role allows for construction of an AMI for an application.


### Requirements

Since this role interacts with AWS, we require the `boto` library be installed.


### Variables

#### Required Variables

These variables must be set in order for the role to run.

```YAML
aws_access_key:     # Or the AWS_ACCESS_KEY_ID environment variable can be set
aws_secret_key:     # Or the AWS_SECRET_ACCESS_KEY environment variable can be set
aws_region:         # Or the AWS_REGION environment variable can be set

ami_builder_base:   # ID of the AMI to build off of
```

#### Common Variables

These are variables shared across some AWS-related roles that are used to provide defaults for other variables.

```YAML
aws_application_name: my-application
aws_tags: {}
```

#### Other Variables

In order to access the instance used to create the AMI we first have to create an SSH key to access the instance with.

```YAML
# The path to the SSH public key for accessing the AMI builder
ami_builder_key_file: "~/.ssh/id_rsa.pub"

# Controls the name of the SSH key used to access the AMI builder instance
ami_builder_key_name: "{{ aws_application_name }}-key"
```

You can control the attributes of the instance used to create the AMI with the following variables.

```YAML
ami_builder_instance_type: t2.nano

ami_builder_default_tags:
  application: "{{ aws_application_name }}"
  role: ami-builder
ami_builder_tags: "{{ ami_builder_default_tags | combine(aws_tags) }}"


### Usage

The role should be included 2 separate times. Once to create the AMI builder, and once to finalize the AMI.

```YAML

- hosts: localhost
  connection: local

  roles:
    - { role: cdriehuys.ami-builder, ami_builder_task: create }

- hosts: amibuilder

  tasks:
    - debug:
        msg: Perform some task on the AMI builder

- hosts: localhost
  connection: local

  roles:
    - { role: cdriehuys.ami-builder, ami_builder_task: finalize }
```


### License

MIT


### Author

Chathan Driehuys (cdriehuys@gmail.com)
