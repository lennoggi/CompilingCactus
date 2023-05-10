# Setting up a Spack environment for CarpetX on Frontera

1. Clone Spack under `$WORK` (`$HOME` has limited space and is slower):
   ```
   cd $WORK
   git clone -c feature.manyFiles=true git@github.com:spack/spack.git
   ```
   **NOTE:** if you already cloned Spack and want to update the package list and info (`spack info <package>`), then update the `spack` directory:
   ```
   cd spack
   git pull
   ```

2. Start Spack:
   ```
   . spack/share/spack/setup-env.sh
   ```
   **NOTE:** this must be done every time you log into the machine

3. Create and activate a Spack environment:
   ```
   spack env create carpetx-cpu
   spacktivate carpetx-cpu -p
   ```

4. Find all compilers available on the machine and add them to `$WORK/spack/var/spack/environments/carpetx-cpu/spack.yaml`:
   ```
   spack compiler find
   ```
   and check that they have actually been found:
   ```
   spack compilers
   ```

5. CarpetX requires a compiler supporting `-std=c++17`, so install the most recent version of `gcc`:
   - Request an interactive session to avoid running out of memory during the installation:
     ```
     idev -m 120 -A <my_allocation>
     ```
   - See if there are any options you want to enable/disable for the new compiler:
     ```
     spack info gcc
     ```
     or use `spack versions gcc` to just list the available versions
   - Install the most recent version of `gcc` (e.g. `gcc@13.1.0`):
     ```
     spack install --add gcc@13.1.0%gcc@4.8.5  # gcc@8.3.0 does not work
     ```
   - Exit the interactive session (`ctrl+d`)
   - Update the compiler list:
     ```
     spack compiler find
     spack compilers
     ```

7. Install all the needed packages
   - Make sure your package list is up to date (see 1):
     ```
     cd <path/to/spack/directory>/spack
     git pull
     ```
   - For all packages you want to install, check the available versions and options to be enabled/disabled:
     ```
     spack info <package>
     ```
   - Add and install the needed packages (consider using again an interactive session):
     ```
     spack install --add zlib@1.2.13%gcc@13.1.0
     spack install --add yaml-cpp@0.7.0%gcc@13.1.0
     spack install --add nsimd@3.0.1%gcc@13.1.0
     spack install --add hdf5@1.14.0+cxx+fortran+hl+mpi+threadsafe%gcc@13.1.0
     spack install --add fftw@3.3.10+openmp+pfft_patches%gcc@13.1.0
     spack install --add silo@4.10.2%gcc@13.1.0
     spack install --add adios2@2.9.0+hdf5+pic+python+shared%gcc@13.1.0
     spack install --add openpmd-api@0.15.1+python%gcc@13.1.0
     spack install --add ssht@1.5.2%gcc@13.1.0
     spack install --add petsc@3.19.1+fftw+hwloc+openmp%gcc@13.1.0
     spack install --add amrex@23.05+hdf5+openmp+particles+shared%gcc@13.1.0
     ```
     **IMPORTANT:** keep this list up to date by:
     1. keeping the master `spack` directory up to date (`git pull` in there)
     2. running `spack info <package>` for every package in the list before (re-)installing a given <package>

7. Before logging out of the machine, good practice requires deactivating the environment:
   ```
   despacktivate
   ```
