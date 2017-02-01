# UsefulStuff

Install Docker
Install Nvidia-Docker

The Dockerfile provided in this repository, starts from nvidias image of Ubuntu 14.04 with CUDA 7.5 and CUDNN 5 and installs everything neccessary to build caffe in this computer.

To use the dockerfile and build a new image from it we use the following (dockerfilePath is the path to the folder containing the Dockerfile and repositoryName/projectName:tagName are self explanatory and will be the name of the image finally build (if the build is successful)): 
sudo nvidia-docker build -t "repositoryName/projectName:tagName" dockerfilePath
in our case:
sudo nvidia-docker build -t "caffe/gpu:caffeInstalled" standalone/gpu/stratosTest

To see all currently existing local docker images we run: 
sudo nvidia-docker images

To see all currently running docker containers we run: 
sudo nvidia-docker ps -l

If we want to run a specific image we type the following: 
sudo nvidia-docker run -ti --rm caffe/gpu:caffeInstalled

If we want to run a specific image using only a subset of the GPUs, we use the ENVIRONMENT VARIABLE NV_GPU:
sudo NV_GPU=0 nvidia-docker run -ti --rm caffe/gpu:caffeInstalled

If we want to mount a folder of the host from within the docker container, we use the flag "-v" and the format hostfolder:containerfolder  as in the following line:
sudo nvidia-docker run -v /home/stratos/APIDIS:/workspace/APIDIS -ti --rm caffe_rtpose/gpu:rtposeInstalled

In order to mount a remote folder (this isn't docker related as we run it on the host and is only here for completeness) 
sshfs user@remoteComputerNameOrIP:pathToRemoteFolder pathToLocalFolder


After building BLVC\Caffe we clone and install

