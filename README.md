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