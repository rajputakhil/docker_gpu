# NVIDIA Container Runtime for Docker

This tutorial will help users to use Nvidia Docker image (tensorflow-gpu) on Ubuntu with a machine having GTX 1080ti Nvidia Graphic Card.

## 1. Install Ubuntu 18.04

## 2. Install NVIDIA driver - Proprietary GPU Driver

https://www.mvps.net/docs/install-nvidia-drivers-ubuntu-18-04-lts-bionic-beaver-linux/

### i – Clean the system of other Nvidia drivers

Before we start installing the correct driver, we need to clean the system of any previously installed driver that might create software issues.

We can do that by using the following command:

    sudo apt-get purge nvidia*

### ii – Check the latest driver version for our Nvidia GPU

We need to check what version of the latest drivers is our graphic card capable of running.

We can check this by visiting the following page and see what is the correct driver version for our graphic card.

Afterwards, we will visit the graphic driver PPA homepage here  and see if our graphic card is compatible with the drivers present in the repository.

### iii – Add the Nvidia graphic card PPA

We can add the graphic-driver PPA using the following command:

    sudo add-apt-repository ppa:graphics-drivers

### iv - Prepare the system for the installation

To retrieve information about the latest version of the packages listed in the repository we run:

    sudo apt-get update

Before we proceed to the next step, we need to install screen to make sure that our installation is not interrupted by network fluctuations that might close our SSH session.

    sudo apt-get install screen

We login into screen using the command:

screen

### v – Install the Nvidia GPU driver

Using the previously aquired information from step 2, we download and install the latest Nvidia driver supported by our GPU. Please note that the command is different for each graphic card, depending on the driver available for it:

    sudo apt-get install nvidia-XYXYX

    sudo apt-get install nvidia-390

( Ex. nvidia-390 is the latest driver version for the GTX 1xxx series).

After the installation is done, we need to reboot the computer:

    sudo reboot

### vi – Verify Nvidia Driver installation

After the system has resumed, we can check if the driver has been installed correctly:

    lsmod | grep nvidia

or

    nvidia-smi

If there is no output, most likely your your driver install process has failed. It is also possible that the GPU driver is not available in your system driver database. You can check if your system is running on the open source driver nouveau. If there is no output for nouveau, then your installation has succeded.

    lsmod | grep nouveau

## 3. Install Docker

## 4. Install nvidia-docker2

(https://github.com/NVIDIA/nvidia-docker/blob/master/README.md)

#### If you have nvidia-docker 1.0 installed: we need to remove it and all existing GPU containers
    docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
    sudo apt-get purge -y nvidia-docker

#### Add the package repositories
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list
    sudo apt-get update

#### Install nvidia-docker2 and reload the Docker daemon configuration
    sudo apt-get install -y nvidia-docker2
    sudo pkill -SIGHUP dockerd

#### Test nvidia-smi with the latest official CUDA image
    docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi

## 5. Install Tensorflow Nvidia-container image

    docker pull nvcr.io/nvidia/tensorflow:18.08-py3

## 6. To run the container, run the following

    sudo nvidia-docker run -it --rm -v /absolute path:/workspace nvcr.io/nvidia/tensorflow:18.08-py3

test out if tensorflow-gpu is working

    python3
    >>from tensorflow.python.client import device_lib
    >>print(device_lib.list_local_devices())

## 7. Install and Create virtualenvironment

    python3 -m pip install --user virtualenv
    python3 -m virtualenv VIRTUAL_ENV_NAME

## 8. Activate it

    source VIRTUAL_ENV_NAME/bin/activate

## 9. Install GPU version of ludwig from akhil's repository:

    git clone --depth=1 https://github.com/rajputakhil/ludwig.git \
    cd ludwig/ \
    pip install -r requirements.txt \
    python -m spacy download en \
    python setup.py install

## 10. Run the follwoing 'ludwig' code

    ludwig train --data_csv chitchat.csv --model_definition_file model_definition.yaml
