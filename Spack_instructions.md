# Setting up a Spack environment

1. Clone Spack:
   ```
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
   spacktivate -p <my_environment>
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
   spack install gcc@13.1.0 %gcc@4.8.5  # gcc@8.3.0 does not work on Frontera
   ```
   and add the new compiler to `</path/to/spack/directory>/spack/var/spack/environments/<my_environment>/spack.yaml`:
   ```
   spack compiler find
   spack compilers
   ```

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
