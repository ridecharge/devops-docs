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

## Docker Containers
* [statsd-docker](https://github.com/ridecharge/statsd-docker) - Container to provide the statsd/librato service to other services.
* [nginx-docker](https://github.com/ridecharge/nginx-docker) - Nginx container to be linked to customer app containers to provide proxy and other web configurations. Should be linked against the consul container for configuration.
* [consul-docker](https://github.com/ridecharge/consul-docker) - Container to provide either the consul cluster service or local proxy service. This is determined based on whether an instance is tagged with Role:consul or not. 
* [aws-startup-utils-docker](https://github.com/ridecharge/aws-startup-utils-docker) - Collection of startup scripts to allow for various things at instance startup, including but not limited to DNS registration, NAT Monitoring/Failover, and ENI Attachment. These will be typically limited to special infrastructure (NAT, NTP, Bastion).
* [logging-docker](https://github.com/ridecharge/logging-docker) - container provides syslog-ng to rest of the system.

## Custom Services
These are custom services used to help expose information and manage our environments. These will also include Dockerfiles to build deployable containers.
* [cf-versions](https://github.com/ridecharge/cf-versions) - This service will allow for easy viewing of services deploy version and endpoints.

## Config with consul and confd
consul is service for centralization of configuration and service discovery. confd is a tool which allows for easy popuating templates from consul's key/value store. Custom apps will use the key/value api to pull their configurations from the environment. 
* [curbd](https://github.com/ridecharge/curbd) - This repo holds static json config files to be stored in consul as well as well as scripts to populate consul from the json files and CloudFormation outputs for generated resources.

## Ansible Roles
These's are ansible-galaxy roles used to provision AMI's and docker images.  These typically are only created when more complex configuration is needed than simple shell configuration by docker and packer.  They will typically be self contained with no dependencies due to how ansible-galaxy works.  Packer AMI descriptions and Dockfiles will typically be where roles are pulled together to make up a machine.
* [base-ansible-role](https://github.com/ridecharge/base-ansible-role) - This role is used in building the base-curbix image. It includes base packages, and user configurations.
* [nat-ansible-role](https://github.com/ridecharge/nat-ansible-role) - This role configures IP tables for our nat services.
* [ntp-ansible-role](https://github.com/ridecharge/ntp-ansible-role) - This role configures either the NTP service configuration for the ntp-curbix images or NTP clients for the base-curbix image
* [logging-ansible-role](https://github.com/ridecharge/logging-ansible-role) - This role configures logrotate and syslog-ng. It is used in the logging-docker container to setup a shared syslog-ng server and in the base-curbix image to forward system logs to the container syslog-ng server. 
* [python3-ansible-role](https://github.com/ridecharge/python3-ansible-role) - This role installs python3 and pip3 as well as any additional pip packages provided in a playbook.


