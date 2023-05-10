# Setting up a Spack environment for CarpetX on Frontera

1. Clone Spack under `$WORK` (`$HOME` has limited space and is slower):
   ```
   cd $WORK
   git clone -c feature.manyFiles=true git@github.com:spack/spack.git
   ```

2. Start Spack:
   ```
   . /spack/share/spack/setup-env.sh
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
   - Update the compiler list:
     ```
     spack compiler find
     spack compilers
     ```

6. For all packages you want to install, check the available versions and options to be enabled/disabled:
   ```
   spack info <package>
   ```
   **NOTE:** `spack versions <package> only lists the available versions (checksummed and not checksummed)

7. Install all the needed packages:
   ```
   spack install --add fftw@3.3.10+openmp+pfft_patches
   spack install --add hdf5@1.14.0+cxx+fortran+hl+ipo+mpi+threadsafe%gcc@13.1.0
   spack install --add amrex@23.05+hdf5+openmp+particles%gcc@13.1.0
   ```



# FIXME
6. Add any packages you want to install to `</path/to/spack/directory>/spack/var/spack/environments/<my_environment>/spack.yaml` (see e.g. `CompilingCactus/cactusAMReX/Frontera_GNU_Spack_CPU/spack.yaml` for an example)

7. Build all packages inside the environment
   ```
   spack install
   ```
   **NOTE:** for fresh installations, this will first *concretize* all packages. If you update `spack.yaml` after the first installation and you want all packages to be reconcretized before attempting another installation, run:
   ```
   spack concretize --force --fresh
   spack install
   ```

8. Check that everything went fine. For example, if `gcc@13.1.0` was supposed to be installed and you want to use it, type
   ```
   spack load `gcc@13.1.0`
   which gcc
   ```
   and make sure this gives the expected answer.

7. Before logging out of the machine, good practice requires deactivating the environment:
   ```
   despacktivate
   ```
