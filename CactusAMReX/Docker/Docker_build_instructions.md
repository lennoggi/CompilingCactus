# Docker basics
See https://docs.docker.com/get-started for a thorough introduction to Docker. What follows is a minimal set of instructions to using Docker.
**NOTE** All the following commands may need root permissions to be executed (i.e. `sudo` is needed), depending on the host machine setup
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
2. Once inside the container (under `/root`), setup and build Cactus
   - CPU build
     ```
     mkdir ET_2022_11_CarpetX_CPU
     cd ET_2022_11_CarpetX_CPU
     curl -kLO https://raw.githubusercontent.com/gridaphobe/CRL/ET_2022_11/GetComponents
     chmod +x GetComponents
     ./GetComponents --parallel --no-shallow https://bitbucket.org/eschnett/cactusamrex/raw/master/azure-pipelines/carpetx.th
     cd Cactus
     ./simfactory/bin/sim setup-silent
     ```
     Possibly edit the local definitions
     ```
     vim simfactory/etc/defs.local.ini
     ```
     Build
     ```
     ./simfactory/bin/sim build ET_2022_11_CarpetX_CPU -j8 --optionlist /usr/carpetx-spack/local-cpu.cfg --thornlist thornlists/carpetx.th
     ```
   - GPU build
     ```
     mkdir ET_2022_11_CarpetX_GPU
     cd ET_2022_11_CarpetX_GPU
     curl -kLO https://raw.githubusercontent.com/gridaphobe/CRL/ET_2022_11/GetComponents
     chmod +x GetComponents
     ./GetComponents https://bitbucket.org/eschnett/cactusamrex/raw/master/azure-pipelines/carpetx.th
     cd Cactus
     ./simfactory/bin/sim setup-silent
     ```
     Possibly edit the local definitions
     ```
     vim simfactory/etc/defs.local.ini
     ```
     Build
     ```
     ./simfactory/bin/sim build ET_2022_11_CarpetX_GPU -j8 --optionlist /usr/carpetx-spack/local-gpu.cfg --thornlist thornlists/carpetx.th
     ```
