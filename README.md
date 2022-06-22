# CAPITest Objective:
Develop and test a way to use a CAPI/openCAPI adapter in an Openshift App environment.

## Prerequisite:
You need to have the OpenCAPI_as_a_Service Device Plugin up and running
(This device plugin will advertise the OpenCAPI cards available in the cluster, will allocate the OpenCAPI cards to the Pod requesting them, etc)

## For generating the docker hub capitest images, run the following script: Build-capitest.sh
 --> It must be run on a Linux on Power system with development tools installed
The Build-capitest.sh script is here to generate the capitest image (thanks to the Dockerfile) and push it into docker hub.

## Way to use it:
You need to start an OpenShift Pod running a container with this CAPITest docker image.
To do so, have a look at the scripts in ./OpenShift directory (scripts you need to run from an OpenShift client system with access to the cluster)
  - user_CAPITest.bash: a script that will create a specific namespace for the user, a CAPITest deployment with a Pod and a CAPITest container, etc (user_CAPITest -h for details on how to use it).
  - start_CAPITest.bash: The script I was using for testing the CAPITest image, so it only creates a POD/Container into the fabriceproject namespace, allowing me to choose different cards and definition yaml files.

## Notes
The last line of the Dockerfile is "CMD /usr/local/bin/Run_The_APP.bash", so the container automatically runs Run_The_APP.bash when starting.
 -> This makes the container:
  - first "reset" (clean) the card by installing a generic Partial Reconfiguration image into the card
  - stay alive (as the command run never ends) if the reset was successful. 
(If you use something like "CMD /bin/bash", the container doesn't stay alive and OpenShift tries to restart it again and again)

Initially, the CAPITest image was using the container-modified https://github.com/OpenCAPI/libocxl library
(the master branch thanks to the following commit: https://github.com/OpenCAPI/libocxl/commit/e9e32473cf85717482e9d0f63a3c7a26e266fbb9 )
 -> Now the libocxl library taken from the linux distribution is OK (as the OpenCAPI system global_mmio_area file that needs to be writable is now RW-mounted under /sys directory)

