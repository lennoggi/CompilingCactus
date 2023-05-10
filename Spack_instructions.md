# Setting up a Spack environment
1. Clone Spack:
   ```
   git clone -c feature.manyFiles=true git@github.com:spack/spack.git
   ```
2. Start Spack:
   ```
   . ~/spack/share/spack/setup-env.sh
   ```
   **NOTE:** this must be done every time you log into the machine
3. Create and activate a Spack environment:
   ```
   spack env create <my_environment>
   spacktivate -p <my_environment>
   ```
4. Copy the `spack.yaml` file you want to use to build packages inside the environment into the appropriate location:
   ```
   cp </path/to/desired/yaml/file>/spack.yaml </path/to/spack/directory>/spack/var/spack/environments/carpetx-cpu
   ```
5. Build all packages listed in `spack.yaml` inside the environment
   ```
   spack install
   ```
   **NOTE:** for fresh installations, this will first *concretize* all packages. If you update `spack.yaml` after the first installation and you want all packages to be reconcretized before attempting another installation, run:
   ```
   spack concretize --force --fresh
   spack install
   ```
6. Check that everything went through by e.g. typing
   ```
   which gcc
   ```
   and make sure the binary is located in the place dictated by `spack.yaml`
7. Before logging out of the machine, good practice requires deactivating the environment:
   ```
   despacktivate
   ```
