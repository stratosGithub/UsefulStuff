# Installing Docker and Compiling and running CMU Pose estimation code

## Docker 
Install Docker
Install Nvidia-Docker

The Dockerfile provided in this repository, starts from nvidias image of Ubuntu 14.04 with CUDA 7.5 and CUDNN 5 and installs everything neccessary to build caffe in this computer.


To use the dockerfile and build a new image from it we use the following (dockerfilePath is the path to the folder containing the Dockerfile and repositoryName/projectName:tagName are self explanatory and will be the name of the image finally build (if the build is successful)): 
<pre>sudo nvidia-docker build -t "repositoryName/projectName:tagName" dockerfilePath</pre>
in our case:
<pre>sudo nvidia-docker build -t "caffe/gpu:caffeInstalled" standalone/gpu/stratosTest</pre>

To see all currently existing local docker images we run: 
<pre>sudo nvidia-docker images</pre>

To see all currently running docker containers we run: 
<pre>sudo nvidia-docker ps -l</pre>

If we want to run a specific image we type the following: 
<pre>sudo nvidia-docker run -ti --rm caffe/gpu:caffeInstalled</pre>

If we want to run a specific image using only a subset of the GPUs, we use the ENVIRONMENT VARIABLE NV_GPU:
<pre>sudo NV_GPU=0 nvidia-docker run -ti --rm caffe/gpu:caffeInstalled</pre>

If we want to mount a folder of the host from within the docker container, we use the flag "-v" and the format hostfolder:containerfolder  as in the following line:
<pre>sudo nvidia-docker run -v /home/stratos/APIDIS:/workspace/APIDIS -ti --rm caffe_rtpose/gpu:rtposeInstalled</pre>

In order to mount a remote folder (this isn't docker related as we run it on the host and is only here for completeness) 
<pre>sshfs user@remoteComputerNameOrIP:pathToRemoteFolder pathToLocalFolder</pre>

After we've installed everything we need it's time to install the code from the repository https://github.com/CMU-Perceptual-Computing-Lab/caffe_rtpose that does the pose estimation:
<pre>git clone https://github.com/CMU-Perceptual-Computing-Lab/caffe_rtpose.git</pre>

Our version of Ubuntu is 14.04 (we specified that in our Dockerfile) and therefore we need to change the file Makefile.config.Ubuntu14.example (which is the makefile that the installation script install_caffe_and_cpm.sh calls) 
We have to comment lines 40 and 41 since the architectures sm_60 and sm_62 are not supported from CUDA 7.5 .
OpenCV and CUDA are installed in the right positions and therefore we don't really need to change anything else.
Now we can install everything just by runnning ./install_caffe_and_cpm.sh 

After we've installed this, if we exit our container session we are going to lose all changes that we've made to our docker file. In order to save this information we have to do the following: 
<ol><li>Open another terminal and list all currently running containers: 
<pre>sudo nvidia-docker ps -l</pre></li>
<li>Note the Container ID (it's going to be something like 10f9d29e7e29)and run: 
<pre>sudo nvidia-docker commit ContainerID caffe_rtpose/gpu:rtposeInstalled</pre>
Here we named our new image as caffe_rtpose/gpu:rtposeInstalled. </li>
</ol>

If we want to change the name of any of our images we can do following: 
<pre>sudo nvidia-docker tag oldName:tagOld newName:tagNew</pre>

Finally since we've finished installing everything neccessary we can now run again our image:
<pre>sudo nvidia-docker run -v /home/stratos/APIDIS:/workspace/APIDIS -ti --rm caffe_rtpose/gpu:rtposeInstalled</pre>

And since our container is now running we can test the binary rtpose.bin on our images:

<pre>./build/examples/rtpose/rtpose.bin --image_dir /workspace/APIDIS/OriginalDatasetFramesUndistortedResized/camera1 --no_frame_drops --write_frames /workspace/APIDIS/output/camera1 --write_json /workspace/APIDIS/output/camera1 --num_scales 3 --scale_gap 0.15  --no_display</pre>



UPDATE: Network issues with the latest version of docker. Solution: http://stackoverflow.com/a/40516974

UPDATE2: To forward jupyter and tensorboard ports 8888 and 6006: 
<pre> sudo nvidia-docker run -p 8888:8888 -p 6006:6006 -it --rm -v hostFolder:dockerFolder DOCKER_IMAGE_NAME </pre>
In our case: 
<pre>sudo nvidia-docker run -p 8888:8888 -p 6006:6006 -it --rm -v /media/stratos/DataPartition/Dropbox/ProgrammingUCL/DeepLearning:/workspace/DeepLearning -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY stratos/tensorflow1.0:withJupyter</pre>
To run jupyter you need to specify -ip 0.0.0.0: 
<pre>jupyter notebook --ip 0.0.0.0 --no-browser </pre>

UPDATE3: Docker for GUI-based environments
http://stackoverflow.com/questions/24095968/docker-for-gui-based-environments

docker run -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=unix$DISPLAY TheImage




