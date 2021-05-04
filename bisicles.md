# BISICLES

## Build Instructions

For much more complete and comprehensive instructions, consult the actual [BISICLES build instructions](http://davis.lbl.gov/Manuals/BISICLES-DOCS/index.html)

This is intended to be a version that I can follow easily based on problems I've encountered in the past. 
This information is up to date as of May 2021. 

To build BISICLES you need:
* GNU/Linux system
* C++ compiler
* FORTRAN compiler
* GNU compatible make
* subversion 
* perl

Recommended:
* Python
* VisIt
* netcdf

### 1. Debian/Ubuntu

This is probably the easiest system to compile BISICLES on, if you don't have Ubuntu you can use a virtual machine or the Windows Subsystem for Linux. If you have a verion of Ubuntu that is older than 2016 you may need to follow the generic build instructions. 

#### Installing prerequisites and packages
If it isn't already on your machine:

`sudo apt-get install make`

You can then install the mpi environment, netcdf, hdf5 and python by: 

`sudo apt-get install subversion g++ gfortran csh mpi-default-bin mpi-default-dev libhdf5-mpi-dev libhdf5-dev hdf5-tools libnetcdff-dev libnetcdf-dev python3 python3-dev
 libpython3-dev`
 
 (You can combine these 2 commands if you want)
 
#### Checking out the source code

To get Chombo you need an [ANAG repository account](https://anag-repo.lbl.gov/).
You need to choose a username and password, that will be asked when you check out the source code. (You might want to write it down if you may need to compile BISICLES more than once and keep forgetting like I do. That being said, you can just make a new account). 

* Create a new directory where you BISICLES to live, e.g.:
  
  `mkdir -p [/path/to/bisicles]`

* Export the path to this directory so you can refer to it easily:
  
  `export $BISICLES_HOME=[/path/to/bisicles]`

  This will mean that you can use `$BISICLES_HOME` to refer to that path without having to type it out each time (and also you can copy and paste instructions).
  
* Change into the new directory:
  
  `cd $BISICLES_HOME`
  
* Then get the source code:

  `svn co https://anag-repo.lbl.gov/svn/Chombo/release/3.2 Chombo`

  `svn co https://anag-repo.lbl.gov/svn/BISICLES/public/trunk BISICLES`

  The BISICLES version here is the main development branch rather than the current release as the current release doesn't support Python3 (as of May 2021). 

#### Installing PETSc

You can skip this if you don't want to use PETSc. If you have no idea what PETSc is or if you're not sure if you want it, then install PETSc. It seems to be the build to do these days. 

* Download PETSc:
  
  In `$BISICLES_HOME`:
  
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
