# BISICLES

For a complete and comprehensive set of instructions, consult the actual [BISICLES build instructions](http://davis.lbl.gov/Manuals/BISICLES-DOCS/index.html)

This is intended to be a walkthrough that I can follow easily based on problems I've encountered in the past. 
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

## Walkthroughs for:
1. [Debian/Ubuntu systems](https://clairedons.github.io/bisicles_debian)
2. [KNMI HPC](https://clairedons.github.io/bisicles_knmi)

## Issues, warning and other things you may want to do:

* **You may want to permanently add the path to `$BISICLES_HOME` to make things easier when doing things with BISICLES.** 

If using bash then you can add:

`export BISICLES_HOME=[/path/to/bisicles]`

to your `.bash_profile` or `.bashrc` files found in your home directory.

* **PETSc gives you a warning about the environment variable needing to be the current directory**

This might pop up if you are recompiling BISICLES or already have a PETSc path exported or added to your `.bashrc`, `.bash_profile` or equivalent file. You can fix it by exporting the path again to the directory you are installing PETSc in. 

`export PETSC_DIR=$BISICLES_HOME/[petsc-dir]`

* **You get this warning:**

        None of the TCP networks specified to be included for out-of-band communications
        could be found:

          Value given: ib0,ipoptl

        Please revise the specification and try again.

I think this is something to do with openMPI and is just a warning, if this pops up it means that the code might run a bit slower than it otherwise would but it'll still run. 

* **Parallel HDF5 isn't working due to file locking things:**

        ADIOI_Set_lock:: Function not implemented
        ADIOI_Set_lock:offset 0, length 1
        application called MPI_Abort(MPI_COMM_WORLD, 1) - process 0
        This requires fcntl(2) to be implemented. As of 8/25/2011 it is not. 
        Generic MPICH Message: File locking failed in ADIOI_Set_lock(fd 9,cmd F_SETLKW/7,type F_RDLCK/0,whence 0) 
        with return value FFFFFFFF and errno 26.
        - If the file system is NFS, you need to use NFS version 3, 
        ensure that the lockd daemon is running on all the machines, and mount the directory with the 'noac' option 
        (no attribute caching).
        - If the file system is LUSTRE, ensure that the directory is mounted with the 'flock' option.

On the KNMI HPC, this seems to be due to how the Lustre file system may be mounted and/or only seems to be a problem when compiling with the Intel MPI. I don't really have a solution to this other than to recompile with openMPI if possible. 

[Ticket with the same issue](https://trac.mcs.anl.gov/projects/parallel-netcdf/ticket/21)
