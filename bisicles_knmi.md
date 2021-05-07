
## KNMI HPC (Intel mpi)

This walkthrough is for if you want to compile BISICLES on the HPC at KNMI but can be more broadly related to any system that uses the [Intel MPI library](https://software.intel.com/content/www/us/en/develop/documentation/mpi-developer-guide-linux/top.html) (instead of [openMPI](https://www.open-mpi.org/)) and where you may not have sudo rights. 

The instructions are essentially the same to the [generic build instructions](http://davis.lbl.gov/Manuals/BISICLES-DOCS/readme.html) but with references to Intel MPI. 

 
### 1. Checking out the source code

To get Chombo you need an [ANAG repository account](https://anag-repo.lbl.gov/).
You need to choose a username and password, that will be asked when you check out the source code. (You might want to write it down if you may need to compile BISICLES more than once and keep forgetting like I do. That being said, you can just make a new account). 

* Create a new directory for BISICLES to live in, e.g.:
  
  `mkdir -p [/path/to/bisicles]`

* Export the path to this directory so you can refer to it easily:
  
  `export BISICLES_HOME=[/path/to/bisicles]`

  This will mean that you can use `$BISICLES_HOME` to refer to that path without having to type it out each time (and also you can copy and paste instructions).
  
* Change into the new directory:
  
  `cd $BISICLES_HOME`
  
* Then get the source code:

  `svn co https://anag-repo.lbl.gov/svn/Chombo/release/3.2 Chombo`

  `svn co https://anag-repo.lbl.gov/svn/BISICLES/public/trunk BISICLES`
  
I am using the main development branch here instead of the current release, as the current release doesn't support Python3. However, unlike for the Debian/Ubuntu build, it doesn't matter which version you use. 

### 2. Dependencies
### 3. HDF5
### 4. NetCDF

### 5. Installing PETSc

You can skip this if you don't want to use PETSc. If you have no idea what PETSc is or if you're not sure if you want it, then install PETSc. It seems to be the build to do these days. 

* Download PETSc:
  
  `cd $BISICLES_HOME`:
  
  `git clone -b release https://gitlab.com/petsc/petsc.git petsc`

  The generic instructions tell you to download the "maint" but it doesn't seem to be possible anymore. For some reason your download directory needs to be called `petsc`now or it refuses to install. The [PETSc](https://www.mcs.anl.gov/petsc/index.html) website has useful information on what PETSc is, downloading and installing it. 

* Configure PETSc:

  Make a new directory for the actual installation:
  
  `mkdir -p $BISICLES_HOME/petsc-install`
  
  Change into the directory where you downloaded PETSc and configure:
  
  `cd $BISICLES_HOME/petsc`
  
  `./configure --download-fblaslapack=yes --download-hypre=yes -with-x=0 --with-c++support=yes --with-mpi=yes --with-hypre=yes --prefix=$BISICLES_HOME/petsc-install --with-c2html=0 --with-ssl=0`
  
  Go through the instructions then export the path to the petsc installation, which Chombo will refer to:

  `export PETSC_DIR=$BISICLES_HOME/petsc-install`
  
#### Chombo Configuration
  
* Copy the `Make.defs.local`file to `$BISICLES_HOME`

  `cp $BISICLES_HOME/BISICLES/docs/Make.defs.local $BISICLES_HOME`

* Edit the file so that:

  `BISICLES_HOME=[path/to/bisicles]`

  To reflect be the path to where BISICLES live ( what `$BISICLES_HOME` is set to) then make sure that it contains the following information:


        PRECISION     = DOUBLE
        CXX           = g++
        FC            = gfortran
        MPICXX        = mpiicc
        USE_HDF       = TRUE
        HDFINCFLAGS   = -I/usr/include/hdf5/serial/
        HDFLIBFLAGS   = -L/usr/lib/x86_64-linux-gnu/hdf5/serial/ -lhdf5 -lz
        HDFMPIINCFLAGS= -I/usr/include/hdf5/openmpi/ 
        HDFMPILIBFLAGS= -L/usr/lib/x86_64-linux-gnu/hdf5/openmpi/ -lhdf5  -lz
        cxxdbgflags   = -g -fPIC 
        cxxoptflags   = -fPIC -O2
        fdbgflags     =  -g -fPIC 
        foptflags     = -fPIC -O3 -ffast-math -funroll-loops

* Make a link to the `Make.defs.local`in the place that Chombo expects it:

  `ln -s $BISICLES_HOME/Make.defs.local $BISICLES_HOME/Chombo/lib/mk/Make.defs.local`
  
#### BISICLES Configuration
  
* Next, create a makefile for BISICLES with the name of your machine:

  `cd $BISICLES_HOME/BISICLES/code/mk/`
  
  `uname -n`
  
  `[mymachine]`
  
  `cp Make.defs.template Make.defs.[mymachine]`

* Make sure it contains the following information:

        PYTHON_INC=$(shell python3-config --includes)
        #--ldflags does not contain -lpython for reasons that escape me
        PYTHON_LIBS=-lpython3.8 $(shell python3-config --ldflags)
        NETCDF_HOME=$(shell nc-config --prefix)
        NETCDF_LIBS=-lnetcdff -lnetcdf -lhdf5_hl

#### Compiling BISICLES

* To compile a standalone BISICLES (so no filetools etc) you can run this:

  `cd $BISICLES_HOME/BISICLES/code/exec2D`
  
  `make all OPT=TRUE MPI=TRUE USE_PETSC=TRUE`

  This is assuming that you followed all of these instructions, if you chose not to include PETSc then:

  `cd $BISICLES_HOME/BISICLES/code/exec2D`
  
  `make all OPT=TRUE MPI=TRUE`

* To compile all of BISICLES:

  `cd $BISICLES_HOME/BISICLES/code`
  
  `make all OPT=TRUE MPI=TRUE USE_PETSC=TRUE`

  (if not using PETSc leave the PETSc statement out as above)

Then you should have a version of BISICLES that can run problems!

#### Running an Example Problem

#### Analysis
