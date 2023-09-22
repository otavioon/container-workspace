# VSCode Dev Container Workspace

This repository contains a template and a tutorial to create a VSCode Dev Container workspace in a remote machine. It contains a Dockerfile to create a deep learning Pytorch image, and all necessary files to create a workspace in VSCode Dev Container. 

Herein, we will use the following terms:
* **Host machine/Local machine**: the machine where you are running VSCode.
* **Remote machine**: the machine where you are running the docker container.
* **Remote container**: a Docker container running in the remote machine. 

The container will be created in the remote machine, and will use its computational resources (GPUs, CPU cores, memory, *etc*). You will be able to access it from the host machine using VSCode but the container **will not** have access to the host machine file system.


## Table of contents

* [Requirements](#Requirements)
* [Connecting to the remote machine](#Connecting-to-the-remote-machine)
* [Creating the remote container](#Creating-the-remote-container)
* [Troubleshooting](#Troubleshooting)
* [Additional Information](#Contact)

## Requirements

The requirements are different for the host machine and the remote machine.

### Host machine requirements

* [VSCode](https://code.visualstudio.com/) with the [Remote Development extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack) installed.

* Working SSH connection to the remote machine. Please [refer to the connection section](#Connecting-to-the-remote-machine) for more information about how to establish a SSH connection.

* A terminal, `ssh` installed and a working connection to remote machine.

### Remote machine requirements

* [Docker](https://docs.docker.com/engine/install/ubuntu/) installed.

* [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker) installed (optional, if the machine has GPUs).

* `git` installed.


## Connecting to the remote machine

Computational cluster is a set of machines, that can be used to run computationally intensive tasks. The users, using their own machines (host machines), can connect to the cluster using SSH, over the internet. An example of a computational cluster is shown in the figure below.

![Computational cluster](figures/cluster.png)

The cluster is usually composed by a set of nodes, each one with its own computational resources (CPUs, GPUs, memory, *etc*). The user, using SSH, usually access a special node, called login node. This login node is usually not used to run computationally intensive tasks, and is used only to access other nodes in the cluster. The other nodes, called compute nodes, are used to run the tasks and is usually accessed also through SSH.

> **NOTE**: It worth notice that some clusters does not have a login node, and the user can access any node in the cluster, directly.

### How to access a computational node using SSH 

Let's suppose that you want to access a computational node, called `dl-99`, in the cluster. The first step is to access the login node, using SSH. In a local machine's terminal, you may run the following command, replacing `user` by your username and `login_node` by the login node address and `port` by the SSH port (usually 22):

```bash
ssh -p <port> <user>@<login_node>
```

After that, you can access the computational node `dl-99` using the following command:

```bash
ssh dl-99
```

> **NOTE**: Different clusters may have different ways to access the computational nodes. Please, refer to the cluster documentation to know how to access the computational nodes.


 ### How to access a computational node using SSH directly, using SSH

 Instead of writing the user and login node address in the command line every time you want to access the cluster, you can create a SSH configuration file to store this information. This file is usually located in the `~/.ssh/config` file, in Linux, and `C:\Users\XXX\.ssh\config`, in Windows. If the file does not exist, you should create it. After that, you can add the following configuration to the file:

 ```bash
Host cluster-login-node
	HostName <login_node>
	User <user>
	Port <port>

Host cluster-dl-99
	Hostname dl-99
	User <user>
	Port <port>
	ProxyJump cluster-login-node

```

The `cluster-login-node` and `cluster-dl-99` are aliases for the login node and computational node, respectively.

From the local machine, you can try to access the login node using the following command:

```bash
ssh cluster-login-node
```

Or, from the local machine, you can try to access the computational node `dl-99` directly, using the following command:

```bash
ssh cluster-dl-99
```

> **NOTE**: This configuration is essential to use the VSCode Dev Container workspace, since it will use the SSH configuration to access the remote machine.

### How to access a computational node via VSCode

> **Requirements**: You must be able to access the remote machine using SSH, directly, as described in the previous section.

To access the remote machine using VSCode, you must install the [Remote Development extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack) in your local machine. After that, you can open the VSCode command palette (Ctrl+Shift+P) and type `>Remote-SSH: Connect to Host...`. You will be asked to type the SSH address of the remote machine and it will be given some options. If you have configured the SSH configuration file, as described in the previous section, you should select the `cluster-dl-99` option or type it. After that, you will be asked to type the username and password. After that, you will be connected to the remote machine.

Below


## Using the VSCode Dev Container workspace

After connecting to the remote machine using VSCode, you may create the docker container. A docker container is a kind of virtual machine with its own operating system, libraries, *etc*, usually with all pre-requisites to run a specific application. In this case, we will create a container with all pre-requisites to run a deep learning application using Pytorch. This container will run at the top of the remote machine operating system, and will use its computational resources (GPUs, CPU cores, memory, *etc*). You will be able to access it from the host machine using VSCode but the container **will not** have access to the host machine file system. The use of remote container is useful to create a development environment, since you can install all necessary libraries and packages in the container, without the need to install them in the host machine or directly in the remote machine.

To use a remote container, you must follow the steps below, which are described in the following sections:
- Connect with VSCode in the remote machine and create and open a folder in the remote machine. This folder will be your workspace.
- Clone this repository in the workspace folder.
- Create the development container in the remote machine.

### Preparing the workspace

After connecting to VSCode, you will need to set up the remote machine workspace. To do this, you should create a folder in the remote machine. All your work inside the container will be done in this folder. This folder will be shared between the remote machine and the container, but it will not be shared with the host machine. 

At the file explorer tab, click on the `Open Folder` button and open or create a folder in the remote machine. All of your work will be done in this folder, also called root folder. Besides that, all of you will be able to visualize only the files inside this folder, using VSCode. Thus, is important to create a folder that contains all files that you will need to work, such as source code, datasets, *etc*. Use the terminal to copy files from other folders to the root folder.

After that, in the root folder, you should create a `.devcontainer` folder which will contain all files necessary to create the container. This repository contains a `.devcontainer` folder with all necessary files to create a container with Pytorch and other libraries. Thus, we need to copy this folder to the root folder. To do that, you should copy the `.devcontainer` folder from this repository to the `.devcontainer` at your root folder. First, open a terminal in the root folder, using the VSCode terminal button (Control+Shift+`) or going to menu "Terminal --> New Terminal". After that, you should have a terminal opened at your root folder. Then, you may the following command:

```bash
git clone https://github.com/otavioon/container-workspace.git		# Download this repository at the root folder
cp -r container-workspace/.devcontainer .devcontainer				# Copy the .devcontainer folder from git to the root folder
```

After that, you should have a `.devcontainer` folder in your root folder. This folder contains all files necessary to create the container. The `.devcontainer` folder will be hidden in the VSCode file explorer, but you can access it using the terminal. 

### Creating the container

Once you place the `.devcontainer` folder and all necessary files inside the root directory, you will be able to create the container. To do that, you should open the command palette (Control+Shift+P) and type `>Dev Containers: Rebuild Container`. This command will create the container and open the root folder inside the container. Note that files outside root folder will not be visible inside the container, so you should copy all necessary files to the root folder. The first time you run this command, it will take some time to create the container, since it will download the container image from the internet. After that, the container will be created and you will be able to use it.

Note that, besides the packages that comes in the container by default, you are able to install other packages using the terminal and commands like `apt` or `pip`. However, it is worth notice that, if the container goes down, all changes inside container will be lost, except that ones you save them in the root folder. Thus, it is important to save all changes in the root folder, to avoid losing them. Other pip packages is usually installed using the `pip install` command, for example, must be reinstalled every time the container is created. To automate this, you can modify the `.devcontainer/post_start.sh` script to install all necessary packages after the container is created. This script is executed every time the container is created, inside the container.

Besides that, when you close your VSCode, the container still run in the remote machine, allowing you to reconnect to it. You can quickly reconnect, opening the VSCode and going to "File --> Open Recent" and selecting the root folder. If the container, somehow, goes down, you can recreate it using the command `>Dev Containers: Rebuild Container` in the command palette.

##  Troubleshooting

Here we describe some common problems that may occur when using the VSCode Dev Container workspace.


### Checking NVIDIA remote container availability

When building a remote container, you may want to check if the NVIDIA Container Toolkit is available in the remote container. To check if the NVIDIA Container Toolkit is available in the remote container, you may run the following command in the remote container's terminal:

```bash
nvidia-smi
```

If the NVIDIA is correctly installed and working, you should see a output similar to the following:

```bash
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.57.02    Driver Version: 470.57.02    CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla V100-SXM2...  On   | 00000000:00:1E.0 Off |                    0 |
| N/A   32C    P0    41W / 300W |      0MiB / 16160MiB |      0%      Default |
|                               |                      |             Disabled |
+-------------------------------+----------------------+----------------------+
|   1  Tesla V100-SXM2...  On   | 00000000:00:1F.0 Off |                    0 |
| N/A   32C    P0    41W / 300W |      0MiB / 16160MiB |      0%      Default |
|                               |                      |             Disabled |
+-------------------------------+----------------------+----------------------+
```

However, sometimes the NVIDIA Container Toolkit fails to be used in the remote container, even after you already have ran an GPU task. This may happen due to several reasons, many external to the container, such as the NVIDIA module not being loaded in the remote machine, *etc*. In this way, the diagnosis of the problem is not trivial. To help you to diagnose the problem, you may use the following steps:

1. Open a terminal in the local host and connect to the remote machine.

2. Check the status of the NVIDIA driver by running the following command in the remote machine. Check if the output is similar to the above. If some error occurs (output is different from the above), you may need to contact the cluster administrator.
	```bash
	nvidia-smi
	```

3. Now test the NVIDIA Container Toolkit in the remote machine, by running the following command in the remote machine. Check if the output is similar to the above. If some error occurs (output is different from the above), you may need to contact the cluster administrator. 
	```bash
	docker run --rm --gpus all ubuntu:22.04 nvidia-smi
	```

4. Now open a remote container's terminal (in VSCode) and run the following command. Check if the output is similar to the above. If some error occurs, try to restart your container, by rebuilding it. Open the command palette (Control+Shift+P) and type `>Dev Containers: Rebuild Container`. And test again.
	```bash
	nvidia-smi
	```
	> **NOTE**: When rebuilding the container, all installed packages will be lost, except things stored in the root folder. You may need to reinstall all packages.


## Additional Information

This repository is licensed under the GPL3 license.
