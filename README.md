# DevOps 2.0 Documentation
This repository serves as a way to provide common documentation and easy discovery of the other repositories that make up the 2.0 system.

# List of 2.0 Repositories
## Curbformation
CurbFormation is the set of projects which define our full AWS environment using CloudFormation.
* [curbformation](https://github.com/ridecharge/curbformation) - This is the main cf python package that use the templates to make CloudFormation API calls (CRUD)
* [curbformation-templates](https://github.com/ridecharge/curbformation-templates) - These are the json templates which define our CloudFormation environment.
* [curbformation-test](https://github.com/ridecharge/curbformation-tests) - This is a test project meant to be able to quickly validate the state of a given environment.

## Curbix AMIs
These are the AMIs references and launched by our CurbFormation tempates.
* [base-curbix](https://github.com/ridecharge/base-curbix) - The base ami for all other amis and ec2 instances using docker. 
* [ntp-curbix](https://github.com/ridecharge/base-curbix) - This AMI is use for NTP services.
* [nat-curbix](https://github.com/ridecharge/base-curbix) - This AMI is used for NAT services.

## Ansible Roles
These's are ansible-galaxy roles used to provision AMI's and docker images.  These typically are only created when more complex configuration is needed than simple shell configuration by docker and packer.  They will typically be self contained with no dependencies due to how ansible-galaxy works.  Packer AMI descriptions and Dockfiles will typically be where roles are pulled together to make up a machine.
* [base-ansible-role](https://github.com/ridecharge/base-ansible-role) - This role is used in building the base-curbix image. It includes base packages, and user configurations.
* [nat-ansible-role](https://github.com/ridecharge/nat-ansible-role) - This role configures IP tables for our nat services.
* [ntp-ansible-role](https://github.com/ridecharge/ntp-ansible-role) - This role configures either the NTP service configuration for the ntp-curbix images or NTP clients for the base-curbix image
* [logging-ansible-role](https://github.com/ridecharge/logging-ansible-role) - This role configures logrotate and syslog-ng. It is used in the logging-docker container to setup a shared syslog-ng server and in the base-curbix image to forward system logs to the container syslog-ng server. 
* [python3-ansible-role](https://github.com/ridecharge/python3-ansible-role) - This role installs python3 and pip3 as well as any additional pip packages provided in a playbook.