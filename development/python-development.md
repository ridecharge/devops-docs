# Developing Python tools
Currently we have two main python tools.  [curbformation](https://github.com/ridecharge/curbformation) and [curbd](https://github.com/ridecharge/curbd). 

We are going to take a look at how to setup python to make development and use easy.

# Installing Python 
We'll be using pyenv and pyenv-virtual env to maintain our environments. To install run

```shell
brew install pyenv
brew install pyenv-virtualenv
```

Next we'll need to install python 3 with.

```shell
pyenv install 3.4.2
```

## Virtual Envs
Now that we have a version of python 3 installed by pyenv we'll need to setup a virtualenv for our applications.

First in the `curbformation` repo run

```shell
pyenv virtualenv 3.4.2 curbformation
```
 this will create the curbformation virtual env.  Next install the curbformation app for local development.

 ```shell
 pip install -e .
 ```

 This will allow any changes made in the python code to be automatically reflected in the virutalenv.

 Now in the `curbformation-templates` you can set the virutalenv 

 ```shell
 pyenv curbformation
 ```

 and have access to the same environment.

 # Tests
 nosetests is used to run tests and produce code coverage.  Typically there will be a `bin/run_test.sh` file to execute these tests which will run

 ```
 nosetests --with-coverage --cover-inclusive --cover-package=package
 ```