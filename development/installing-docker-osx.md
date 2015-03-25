# Installing Docker on OSX
https://www.docker.com/

We'll be installing boot2docker to enable docker to run on OSX.

## Installation
Make sure you have homebrew install and run the following commands to install docker.

1. brew install boot2docker
2. brew install docker

## Using boot2docker
OSX does not natively support docker. To get around this boot2docker was created which runs a docker daemon inside a minimal virutalbox VM.  Once boot2docker is install run the following to enable docker in your environment.

1. boot2docker init
2. boot2docker up
3. then copy/paste the environment variables it outputs.

## Reinit boot2docker
Occasionally you need to reinit boot2docker.  Run
* boot2docker destroy

and then the commands from before to init it again. 