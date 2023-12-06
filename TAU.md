# TAU Installation

## Pre-requisites

**xrdp on local machine**

xrdp provides a graphical login to remote machines using RDP (Microsoft Remote Desktop Protocol)

```
sudo apt-get install xrdp
```

Make sure to ssh into lab machines using -X flag, for example

```
ssh -X divyanshc@csews16
```

**Program Database Toolkit (PDT)**

```
wget http://tau.uoregon.edu/pdt.tgz
```

```
tar -xf pdt.tgz
```

```
cd pdtoolkit-3.25.1
```

```
./configure
```

```
make
```

```
make install
```

## Installing TAU

```
wget http://tau.uoregon.edu/tau.tgz
```

```
tar -xf tau.tgz
```

```
cd tau-2.33
```

```
./tau_setup
```

A window to configure TAU should pop up. Select the required configuration manually. After setting the configuration, copy the command for running the configuration, for example

```
./configure -bfd=download -dwarf=download -unwind=download -iowrapper -pdt=/users/btech/divyanshc/pdtoolkit-3.25.1/ -mpi -openmp -c++=mpicxx -cc=mpicc -fortran=mpif90 -otf=download -pdt_c++=mpicxx
```

Add `/users/btech/divyanshc/tau-2.33/x86_64/bin` to you PATH. Restart the terminal if needed.

```
make install
```

In TAU, after changing the configuration and the doing make install again, the second install will not overwrite the first install, instead, the two configurations are installed side by side.

For each configuration of TAU, a Makefile is present in `$TAU/<arch>/lib/Makefile.tau-*.` `<arch>` is `x86_64` in our case.

`TAU_MAKEFILE` environment variable determines the configuration used by compiler wrappers.

```
export TAU_MAKEFILE=/users/btech/divyanshc/tau-2.33/x86_64/lib/Makefile.tau-mpi-pdt-openmp
```

**Validating the install**

```
./tau_validate --html ~/tau_install/x86_64/ &> results.html
```

### Running TAU on an example

#### Without Instrumentation

```
cd /users/btech/divyanshc/tau-2.33/examples/mm/
```

```
make
```

```
mpicc -DTAU_MPI -DPTHREADS -pthread -g matmult.c matmult_initialize.c -o matmult-noinst
```

```
mpirun -np 4 ./matmult-noinst
```

```
mpirun -np 4 tau_exec -T mpi -ebs ./matmult-noinst
```

```
pprof -a | less
```

Some data visualizing windows should pop-up after the following command,

```
paraprof
```

By clicking on labels corresponding to different threads and nodes, example node-0, thread-0, we can get the information of time spent by the corresponding thread in the corresponding node on different functions separately.

#### Source instrumentation

```
rm profile.*
```

```
tau_cxx.sh -DTAU_MPI -DPTHREADS -pthread -g matmult.c matmult_initialize.c -o matmult-inst
```

```
mpirun -np 4 ./matmult-inst
```

```
paraprof
```
