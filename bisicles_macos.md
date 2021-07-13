### 1. Mac OS
This is a work in progress I still am trying to get things to work. Useful links:

* [The Chombo wiki page](https://github.com/GRChombo/GRChombo/wiki) which goes into more detail about make files and FAQs
* [Examples of Make.defs.local files for different systems](https://github.com/GRChombo/GRChombo/tree/main/InstallNotes/MakeDefsLocalExamples)

#### Installing prerequisites and packages
You can install many of the dependencies you need through "brew", a guide to installation is found [here](https://docs.brew.sh/Installation).

To compile BISICLES you will need:

`brew install subversion make hdf5 gcc mpich zlib wget perl libomp`

as well as potentially:

`brew install petsc netcdf`

#### HDF5 and NetCDF

The hdf5 version installed through brew only works for a serial build, so if you are planning on a parallel build, you may need to install a parallel hdf5 separately. This can be done following a modified version of the [generic build instructions](http://davis.lbl.gov/Manuals/BISICLES-DOCS/readme.html). 
 
#### Checking out the source code

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

  The BISICLES version here is the main development branch rather than the current release as the current release doesn't support Python3 (as of May 2021). 
  
#### Chombo Configuration

I'm currently only trying to get a serial build to work so some options are commented out (although the compilation gets as far when I include mpi). 
  
* Copy the `Make.defs.local`file to `$BISICLES_HOME`

  `cp $BISICLES_HOME/BISICLES/docs/Make.defs.local $BISICLES_HOME`

* Currently the Make.defs.local contains the following information:
  
      
        PRECISION       = DOUBLE
        # brew install gcc
        CXX             = g++-11
        FC              = gfortran-11
        # brew install libomp
        #OPENMPCC        = FALSE
        # brew install mpich
        #MPI             = FALSE
        #MPI_DIR         = /usr/local/Cellar/mpich/3.4.2
        #MPICXX          = g++-11 -I${MPI_DIR}/include -lmpi
        
        # brew install hdf5 is fine for serial builds
        USE_HDF         = TRUE
        #HDF5_DIR = /usr/local/Cellar/hdf5/1.12.1
        #HDF5_DIR = /Users/clairedonnelly/bisicles/hdf5/serial
        HDFINCFLAGS     = -I/Users/clairedonnelly/bisicles/hdf5/serial/include
        HDFLIBFLAGS     = -L/Users/clairedonnelly/bisicles/hdf5/serial/lib -lhdf5 -lz
        # hdf5 v1.10 library downloaded and built using CMake with --enable-parallel
        # (see also the hdf5_cmake_configure.sh script)
        #HDF5_PARALLEL_DIR = /Users/clairedonnelly/bisicles/hdf5/parallel
        #HDFMPIINCFLAGS  = -I${HDF5_PARALLEL_DIR}/include
        #HDFMPILIBFLAGS  = -L${HDF5_PARALLEL_DIR}/lib -lhdf5 -lz
        
        cxxoptflags     = -march=native -O3
        foptflags       = -march=native -O3
        #syslibflags     = -lblas -llapack -L${MPI_DIR}/lib -framework Accelerate
        
        cxxdbgflags   = -g -fPIC
        #cxxoptflags   = -fPIC -O2 
        fdbgflags     =  -g -fPIC
        #foptflags     = -fPIC -O3 -ffast-math -funroll-loops


* Make a link to the `Make.defs.local`in the place that Chombo expects it:

  `ln -s $BISICLES_HOME/Make.defs.local $BISICLES_HOME/Chombo/lib/mk/Make.defs.local`
  
CURRENT COMPILATION ERROR

ld: symbol(s) not found for architecture x86_64
collect2: error: ld returned 1 exit status
make[2]: *** [testFourthOrderFillPatch2d.Darwin.64.g++-11.gfortran-11.DEBUG.ex] Error 1
make[1]: *** [test/AMRTimeDependent] Error 2
make: *** [libtest] Error 2

  
#### BISICLES Configuration
  
* Next, create a makefile for BISICLES with the name of your machine:

  `cd $BISICLES_HOME/BISICLES/code/mk/`
  
  `uname -n`
  
  `[mymachine]`
  
  `cp Make.defs.ubuntu_20.4 Make.defs.[mymachine]`

* Make sure it contains the following information:

        PYTHON_INC=$(shell python3-config --includes)
        #--ldflags does not contain -lpython for reasons that escape me
        PYTHON_LIBS=-lpython3.8 $(shell python3-config --ldflags)
        NETCDF_HOME=$(shell nc-config --prefix)
        NETCDF_LIBS=-lnetcdff -lnetcdf -lhdf5_hl

#### Compiling BISICLES

* To compile a standalone BISICLES (so no filetools etc) you can run this:

  `cd $BISICLES_HOME/BISICLES/code/exec2D`
  
  `make all`

* To compile all of BISICLES:

  `cd $BISICLES_HOME/BISICLES/code`
  
  `make all`

Then you should have a version of BISICLES that can run problems!
