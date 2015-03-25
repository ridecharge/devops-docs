# Installing Docker on OSX
We'll be installing boot2docker to enable docker to run on OSX.
## Installation
brew install boot2docker
brew install docker
## Using boot2docker
OSX does not natively support docker. To get around this boot2docker was created which runs a docker daemon inside a minimal virutalbox VM.  Once boot2docker is install run the following to enable docker in your environment
1. boot2docker init
2. boot2docker up
then copy/paste the environment variables it outputs.