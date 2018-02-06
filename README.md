# CyVerse Machine Learning
A repository to document all the setup, code, commands, and workflow to leverage GPUs for machine learning. The ultimate goal is to leverage GPUs from CyVerse BisQue, both local GPUs and remote GPUs on other platforms like XSEDE, TACC, etc.

# Overview for Setup of K20 Tower Development
Many scientists want to train machine learning models to recognize features in images. 

# Condor Notes
## Marshalling resources
Condor will recognize how many CPUs are available and assign each to a slot
You can modify number of CPUs to each slot
    * 4 CPUs in 4 separate slots
    * Each get 1/4 CPU, Mem, Disk
    * Greater processing if there are more CPUs in a slot (assuming a single machine or the CPUs are hyperthreaded)
Each slot is independent of the other slots

## Job Submissions
Condor can handle dynamic assignment of CPUs to tasks
Will queue tasks that are requesting resources that aren't available

## Development Machine Learning K20 tower hardware Used
1. 12 cores with hyperthreading
2. 6 core


# Setup of Machine Learning K20 Development Tower
Steps to install condor from scratch.

1. Follow instructions from here https://research.cs.wisc.edu/htcondor/yum/ for your RHEL/CentOS/Debian distribution
    For CentOS 7 create the following repo file /etc/yum.repos.d/htcondor-el7.repo with the following contents

    [htcondor-stable]
    name=HTCondor Stable RPM Repository for Redhat Enterprise Linux 7
    baseurl=http://research.cs.wisc.edu/htcondor/yum/stable/rhel7
    enabled=1
    gpgcheck=1

2. Run "yum update" and "yum install condor". Run "systemctl enable condor" to enable the service.

3. Edit /etc/condor/condor_config.local

    NUM_SLOTS=1
    NUM_SLOTS_TYPE_1=1
    SLOT_TYPE_1_PARTITIONABLE=true
    SLOT_TYPE_1 = cpus=100%,disk=100%,swap=100%,gpus=100%
    ASSIGN_CPU_AFFINITY=true

    # These lines add GPU to inventory; these are in a slot with 
    MACHINE_RESOURCE_INVENTORY_GPUs = $(LIBEXEC)/condor_gpu_discovery -properties

    ENVIRONMENT_FOR_AssignedGPUs = CUDA_VISIBLE_DEVICES, GPU_DEVICE_ORDINAL

You assign 1 GPU per slot. Our system has 1 GPU, so we created 1 slot. If we had 4 GPUs, we would create 4 slots. Adjust accordingly for your system.

# Docker Setup
basic steps are: ensure docker user and group exist, add `condor` user to the group; clean up old packages (probably not applicable here since it'll be a new machine); install `yum-plugin-versionlock`, clean up old locks and lock the version to our known-good version (via `yum versionlock delete 0:docker-ce*; yum versionlock add 0:docker-ce-17.03.2.ce-1.el7.centos.x86_64` -- in the above it's a variable from elsewhere), then installs the relevant docker and docker-selinux packages, and sets some various configuration. Then it installs some rsyslog stuff that mostly applies to our services, and installs docker-compose


