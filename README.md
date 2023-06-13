
JFrog Instance - https://bellgeekfest.jfrog.io - AWS US East

Users list - https://docs.google.com/spreadsheets/d/1nI099DVX5JuPMfuwtepYjn8Z6LKLH12ZUET3Vcqr1lo/edit?usp=sharing
Lab details doc - https://docs.google.com/document/d/1klA7QMXC1u3Q_6ddBO37bOZkxNfUTCCkAXfYG35rYx4/edit?usp=sharing

Initial setup
  Verify the access to the instance by logging in
  
  Install JFrog CLI - https://jfrog.com/getcli/
  
  Easiest option - brew install jfrog-cli
  
  Check and confirm - jf -v
  
  Configure CLI with JFrog instance
  
  Add a config - jf config add
  
  Choose a server ID: <unique name> Ex: bellgeekfest
  
  JFrog platform url: https://bellgeekfest.jfrog.io
  
  JFrog access token (Leave blank for username and password/API key): Hit enter
  
  JFrog username: <username>
  
  JFrog password or API key: <password>
  
  Is the Artifactory reverse proxy configured to accept a client certificate? (y/n): n
  
  Use the configured instance - jf config use <server ID>
  
  Health check - jf rt ping


Lab 1
  
  Pre-requisities:
  
  Clone the following npm project from Github - https://github.com/rakeshk24/bell-geekfest
  
  Our project is in the path - root

  Install CLI and configure it with the instance if not done already
  
  On the UI, navigate to Artifactory > Repositories
    Create a npm local repository <username-npm-local>
  
    Create a npm remote repository <username-npm-remote> proxying npm registry
  
    Create a npm virtual repository <username-npm-virtual>, include the above npm local and remote repos to be part of this virtual and then select the local repo to be the ‘default       deployment repository’
  
  Turn on indexing of the local and remote repositories
  
    Navigate to Xray > Indexed resources > Add a repository > Select your repo in the left column and bring it over to the right column > Save
  
  On your local, get to the root of the just cloned project
  
  Lets publish a dummy build to the instance
  
    jf rt bp <username>_npm_build 01
  
  Verify the build info publish on the instance
  
    Navigate to Artifactory > Builds. Verify the just published build info
  
  Turn on indexing of the build
  
    Xray > Indexed resources > Builds > Add a build > Select your build in the left column and bring it over to the right column > Save
  
  From the root of the project:
  
    Npm package manager integration
  
      Run jf npmc
  
      Resolve dependencies from Artifactory? (y/n)? Y
  
      Set Artifactory server ID [bellgeekfest]: hit Return
  
      Set repository for dependencies resolution (press Tab for options): use Tab to select the virtual repository and hit Return
  
      Deploy project artifacts to Artifactory? (y/n)? Y
  
      Set Artifactory server ID [bellgeekfest] hit Return
  
      Set repository for artifacts deployment (press Tab for options): use Tab to select the virtual repository and hit Return
   
    Npm install and build
  
      jf npm install –build-name <username>_npm_build –build-number 02
    
    Publish the built npm pkg to Artifactory
  
      jf npm publish –build-name <username>_npm_build –build-number 02
  
    Collect environment variables
  
      jf rt bce <username>_npm_build 02
  
    Collect information regarding git
  
      jf rt bag <username>_npm_build 02
  
    Publish build info
  
      jf rt bp <username>_npm_build 02
  
    Verify the published build info on the instance and check the scan details in Builds > <your build name> > 02 > Xray data
    
    Scan the build info on your local by Xray - optional
  
      jf bs <username>_npm_build 02
  
Lab 2 - Docker scans

Create a remote repo of type docker that proxies DockerHub
  
  Artifactory > Repositories > Create a repository dropdown > remote > docker > Give the repo a unique name in the repo key text field - <username-docker-remote> > Create
  
Turn on indexing of the repo
  
  Xray > Indexed resources > Ada a repository > Select your repo in the left column and bring it over to the right column > Save
  
  Mouse over on your repo to find the ‘Configure’ option and turn on all 4 options
  
Configure your docker client with the created docker repository
  
  Artifactory > Artifacts > Find and select your remote repository (use search field if you want to) > Hit ‘Set me up’ on the top right
  
  Copy the instructions in the configure section and perform docker login to your repo
  
Pull a docker image from dockerhub
  
  Switch to the pull tab, copy the instruction and replace <DOCKER_IMAGE>:<DOCKER_TAG> with a DockerHub’s image details to pull in the image through the remote repo
  
  Example - python:latest, node:latest
  
  https://hub.docker.com/search?type=image&operating_system=linux&q=
  
Run the docker pull and once the download is done, wait for the scan results in Xray > Scans List > <Your repo> > <The downloaded image name> > Security Issues > Vulnerabilities

Optional - to scan a local docker image after docker push
  
Create a local repo of type docker
  
  Artifactory > Repositories > Create a repository dropdown > local > docker > Give the repo a unique name in the repo key text field - <username-docker-local> > Create
  
Turn on indexing of the repo
  
  Xray > Indexed resources > Add a repository > Select your repo in the left column and bring it over to the right column > Save
  
  Mouse over on your repo to find the ‘Configure’ option and turn on all 4 options
  
Run docker push - to upload an existing docker image on your local
  
  Artifactory > Artifacts > Find and select your local repository (use search field if you want to) > Hit ‘Set me up’ on the top right
  
  Copy the instructions in the configure section and perform docker login to your repo if not already
  
  Switch to the push tab, copy the instruction and replace <IMAGE_ID>, <DOCKER_IMAGE>:<DOCKER_TAG> appropriately.
  
  Then, copy the next command and run docker push by replacing <DOCKER_IMAGE>:<DOCKER_TAG> with appropriate details of the image under consideration
  
Once the download is done, wait for the scan results in Xray > Scans List > <Your repo> > <The downloaded image name> > Security Issues > Vulnerabilities
  
Some CLI commands
Create a generic repository called generic-local
  
Upload a file to the above repo - jf rt u <filename> generic-local
  
Download a file from the repo - jf rt dl <filepath>
