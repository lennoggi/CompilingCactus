# Setting up a Spack environment

1. Clone Spack:
   ```
   cd $WORK
   git clone -c feature.manyFiles=true git@github.com:spack/spack.git
   ```

2. Start Spack:
   ```
   . </path/to/spack/directory>/spack/share/spack/setup-env.sh
   ```
   **NOTE:** this must be done every time you log into the machine

3. Create and activate a Spack environment:
   ```
   spack env create <my_environment>
   spacktivate <my_environment> -p
   ```

4. Find all compilers available on the machine and add them to `</path/to/spack/directory>/spack/var/spack/environments/<my_environment>/spack.yaml`:
   ```
   spack compiler find
   ```
   and check that they have actually been found:
   ```
   spack compilers
   ```

5. *If desired* (e.g. when installing CarpetX, which requires a compiler supporting `-std=c++17`), install some other compiler. For the specific case of `gcc@13.1.0` on Frontera, use:
   ```
   spack versions gcc                   # e.g. the latest safe version is 13.1.0
   spack add gcc@13.1.0%gcc@4.8.5      # The new compiler must be added as a spec in spack.yaml first
   spack install gcc@13.1.0%gcc@4.8.5  # gcc@8.3.0 does not work on Frontera
   ```
   **NOTE:** you can combine adding and installing the compiler with
   ```
   spack install --add gcc@13.1.0%gcc@4.8.5
   ```
   **NOTE:** if the installation produces a memory error, consider performing it on a compute node. For example, on Frontera, you can request an interactive session like this:
   ```
   idev -m 120 -A <my_allocation>
   ```
   and you can then run the above commands.

6. Update the compiler list:
   ```
   spack compiler find
   spack compilers
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
