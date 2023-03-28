Do the following in any job submission script:

1. Activate the Spack environment for CarpetX with
   ```
   . /home/spack/spack/share/spack/setup-env.sh
   spack env activate carpetx
   ```
2. Use `mpiexec`, which should be able to interact with the scheduler (PBS on Green Prairies) and automatically determine the number of MPI ranks to use; this means that the command
   ```
   mpiexec ./<exe>
   ```
   inside a batch script will execute `<exe>` on all the desired MPI ranks without the need for the `-np` option. 
