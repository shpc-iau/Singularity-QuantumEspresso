# QuantumEspresso Singularity Container
A Singularity container, hosting QuantumEspresso 5.4 on CentOS 7, compiled using Intel Compilers (static-linking) and Intel MPI (Version 5.1.3.223).

This repository contains two Singularity Recipe files:
- `Singularity` recipe file contains an image designed to be built in the [Bridge](http://doi.org/10.5281/zenodo.1117442) HPC cluster.
- `Singularity-generic` recipe file is more of a generic image that can be used in any cluster with some extra work.

# Concept
The recipe was created with the following requirements in mind:
- QuantumEspresso should be built using Intel compilers (statically-linked for the most part)
- Installing the whole Intel compilers suite into the container is a bad idea, we should mount the compiler path within the container instead
- Intel MPI dependencies (which cannot be linked statically) are installed into the container

For furhter details, please refer to the full article [Singularity Containers at IAU’s HPC Center: QuantunEspresso as an Example](https://medium.com/@uniquelock/singularity-containers-at-iaus-hpc-center-quantunespresso-56e51308d221)

# Building `Singularity-generic`
Before you can build `Singularity-generic` recipe, you need to change the mount target in the recipe file to match the IP/path of where you store your Intel compilers:

```Bash
echo "Mounting /mountpoint"
mount -t nfs 10.20.30.40:/original/mountpoint /mountpoint 

echo "Sourcing Intel Compilers"
source /mountpoint/parallel_studio_xe_2016.4.072/psxevars.sh
```

# Running the Container
After building the image, QuantumEspresso can be run as follows, using Intel MPI. It is a good idea to set I_MPI_DEBUG to verbose mode so that you can make sure the communications go through the fabric — not the Ethernet — in case your cluster is backed by an InfiniBand interconnect:

```Bash
mpirun -genv I_MPI_DEBUG=5 -hostfile ./hosts -np 16 singularity run ./qe.img pw.x < input_file
```
