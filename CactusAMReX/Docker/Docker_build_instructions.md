# Docker basics
See https://docs.docker.com/get-started for a thorough introduction to Docker. What follows is a minimal set of instructions to get you started with it.

**NOTE** All the following commands may need root permissions to be executed (i.e. `sudo` is needed), depending on the host machine setup. In order to avoid that, follow the instructions at https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user. You may need to log out and log back in and possibly restart your machine for the changes to take place.

- Pulling an image
  ```
  docker pull <image_name>
  ```
- Listing all images on the host machine
  ```
  docker images ls -a
  ```
- Running an image to create a container
  ```
  docker run -ti <image_name> bash
  ```
- Running an image to create a container which is able communicate with the host machine
  ```
  docker run -ti -p 8888:8888 <image_name> bash
  ```
- Listing all running and non-running containers on the host machine
  ```
  docker ps -a
  ```
- Starting and attaching a container (get `<container_ID>` from `docker ps` or `docker ps -a`)
  ```
  docker start <container_ID>
  docker attach <container_ID>
  ```
- Removing a container from the host machine
  ```
  docker rm <container_ID>
  ```
- Removing an image from the host machine (get `<image_ID>` from `docker images ls -a`) 
  ```
  docker image rm <image_ID>
  ```



# Setting up CactusAMReX using Steve Brandt's Docker image
1. Pull and run Steve's image from https://hub.docker.com/r/stevenrbrandt/etworkshop with
   ```
   docker pull stevenrbrandt/etworkshop
   docker run -ti -p 8888:8888 stevenrbrandt/etworkshop bash
   ```

2. Once inside the container, setup a non-root user. This is good practice in general and also prevents `mpirun` and similar commands to complain about running as root.
   - Add the new user
     ```
     adduser <user>
     ```
     and follow the interactive steps
   - Log out of the container
     ```
     exit
     ```
     or
     ```
     ctrl+d
     ```
   - Get the container ID (`<container_id>`)
     ```
     docker ps -a
     ```
   - Start the container
     ```
     docker start <container_id>
     ```
   - Run a bash shell inside the container as the new user
     ```
     docker exec -it -u <user> <container_id> bash
     ```
     **NOTE** Stop the container with
     ```
     docker stop <container_id>
     ```
     after logging out of it if you started it using `docker exec` rather than `docker attach`

3. Build Cactus
   - **CPU build**
     ---
     ```
     cd
     mkdir ET_2022_11_CarpetX_CPU
     cd ET_2022_11_CarpetX_CPU
     curl -kLO https://raw.githubusercontent.com/gridaphobe/CRL/ET_2022_11/GetComponents
     chmod +x GetComponents
     ##./GetComponents --parallel --no-shallow https://bitbucket.org/eschnett/cactusamrex/raw/master/azure-pipelines/carpetx.th
     ./GetComponents --parallel --no-shallow https://raw.githubusercontent.com/jaykalinani/AsterX/main/Docs/thornlist/asterx.th
     cd Cactus
     ./simfactory/bin/sim setup-silent
     ```
     Possibly edit `simfactory/etc/defs.local.ini` like this
     ```
     [default]
     user          = lorenzo
     email         = lorenzo.ennoggi@gmail.com
     allocation    = NO_ALLOCATION
     basedir       = /home/lorenzo/Simulations
     sourcebasedir = /home/lorenzo/ET_2022_11_CarpetX_CPU
     ```
     **NOTE** The following is **NOT NEEDED ANYMORE**, as `TOVSolver` is now being checked out from the thornlist directly. However, it provides an example of how to check out a single thorn without checking out the whole repository where it is contained.
     If you want to checkout the TOV solver thorn from [Jay Kalinani's fork of CactusAMReX](https://bitbucket.org/jaykalinani/cactusamrex), do the following:
     ```
     cd repos
     mkdir TOVSolver
     cd TOVSolver
     git init
     git remote add -f origin https://bitbucket.org/jaykalinani/cactusamrex
     git config core.sparseCheckout true
     echo "TOVSolver" >> .git/info/sparse-checkout
     git pull origin master
     cd ../../arrangements
     mkdir TOVSolver
     cd TOVSolver
     ln -s ../../repos/TOVSolver/TOVSolver TOVSolver
     cd ../..
     ```
     Then edit the `Cactus/thornlists/asterx.th` to compile the thorn
     ```
     # Private thorns
     !TARGET   = $ARR
     !TYPE     = ignore
     !CHECKOUT =
     TOVSolver/TOVSolver
     ```
     Build
     ```
     ./simfactory/bin/sim build ET_2022_11_CarpetX_CPU -j8 --optionlist /usr/carpetx-spack/local-cpu.cfg --thornlist thornlists/asterx.th
     ```
     `PETSc` may not be available. In this case, comment out `CarpetX::PDESolvers` and `CarpetX::Poisson2` from the thornlist and try to recompile.
     There might be problems generating the git source tree for Cactus. In this case, do
     ```
     cd configs/ET_2022_11_CarpetX_CPU/configjar.git
     git config --local user.email "lorenzo.ennoggi@gmail.com"
     git config --local user.name "Lorenzo Ennoggi" 
     ```

   - **GPU build**
     ---
     ```
     cd
     mkdir ET_2022_11_CarpetX_GPU
     cd ET_2022_11_CarpetX_GPU
     curl -kLO https://raw.githubusercontent.com/gridaphobe/CRL/ET_2022_11/GetComponents
     chmod +x GetComponents
     ##./GetComponents --parallel --no-shallow https://bitbucket.org/eschnett/cactusamrex/raw/master/azure-pipelines/carpetx.th
     ./GetComponents --parallel --no-shallow https://raw.githubusercontent.com/jaykalinani/AsterX/main/Docs/thornlist/asterx.th
     cd Cactus
     ./simfactory/bin/sim setup-silent
     ```
     Possibly edit `simfactory/etc/defs.local.ini` like this
     ```
     [default]
     user          = lorenzo
     email         = lorenzo.ennoggi@gmail.com
     allocation    = NO_ALLOCATION
     basedir       = /home/lorenzo/Simulations
     sourcebasedir = /home/lorenzo/ET_2022_11_CarpetX_GPU
     ```
     **NOTE** The following is **NOT NEEDED ANYMORE**, as `TOVSolver` is now being checked out from the thornlist directly. However, it provides an example of how to check out a single thorn without checking out the whole repository where it is contained.
     If you want to checkout the TOV solver thorn from [Jay Kalinani's fork of CactusAMReX](https://bitbucket.org/jaykalinani/cactusamrex), do the following:
     ```
     cd repos
     mkdir TOVSolver
     cd TOVSolver
     git init
     git remote add -f origin https://bitbucket.org/jaykalinani/cactusamrex
     git config core.sparseCheckout true
     echo "TOVSolver" >> .git/info/sparse-checkout
     git pull origin master
     cd ../../arrangements
     mkdir TOVSolver
     cd TOVSolver
     ln -s ../../repos/TOVSolver/TOVSolver TOVSolver
     cd ../..
     ```
     Then edit the `Cactus/thornlists/asterx.th` to compile the thorn
     ```
     # Private thorns
     !TARGET   = $ARR
     !TYPE     = ignore
     !CHECKOUT =
     TOVSolver/TOVSolver
     ```
     Build
     ```
     ./simfactory/bin/sim build ET_2022_11_CarpetX_GPU -j8 --optionlist /usr/carpetx-spack/local-gpu.cfg --thornlist thornlists/asterx.th
     ```
     `PETSc` may not be available. In this case, comment out `CarpetX::PDESolvers` and `CarpetX::Poisson2` from the thornlist and try to recompile.
     There might be problems generating the git source tree for Cactus. In this case, do
     ```
     cd configs/ET_2022_11_CarpetX_GPU/configjar.git
     git config --local user.email "lorenzo.ennoggi@gmail.com"
     git config --local user.name "Lorenzo Ennoggi"  
     ```

4. Edit `simfactory/mdb/machines/generic.ini` in order to match the architecture of your machine, e.g.
   ```
   ppn             = 8
   max-num-threads = 8
   num-threads     = 1
   nodes           = 1
   ```
