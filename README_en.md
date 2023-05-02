# LicheePi 4A

> Building guide & SDK for LicheePi 4A

## Prepare

- Docker

  ```sh
  docker buildx build -t thead-yocto:v1 -f Dockerfile.yocto .

  docker buildx build -t thead-kernel:v1 -f Dockerfile.kernel .
  ```

- git

  ```sh
  git submodule update --init --recursive --depth 1
  ```

- repo (fetch)

  ```sh
  mkdir riscv-yocto && cd riscv-yocto
  repo init -u https://github.com/riscv/meta-riscv -b master -m tools/manifests/riscv-yocto.xml
  repo sync
  repo start work --all
  ```

## Run

- kernel u-boot opensbi driver etc.

  ```sh
  docker run --name runner-kernel -it -v $PWD/external/revyos:/root/workdir thead-kernel:v1
  
  # in docker
  export PATH="$PATH:/root/Xuantie-900-gcc-linux-5.10.4-glibc-x86_64-V2.6.1/bin"
  cd ~/workdir

  # make-env tool
  apt update && apt install ruby
  gem install make-env
  eval $(make-env riscv auto)
  ```

- yocto

  > Machine/Target支持列表

  https://gitee.com/thead-yocto/documents#machinetarget%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8

  ```sh
  docker run --name runner-yocto -it -v $PWD/external/thead-yocto:/home/ubuntu/workdir thead-yocto:v1

  # in docker
  sudo apt-get update && sudo apt-get -y install file python bison flex
  sudo chown -R ubuntu:ubuntu workdir/
  cd workdir/xuantie-yocto
  source openembedded-core/oe-init-build-env thead-build/light-fm

  MACHINE=light-lpi4a bitbake thead-image-linux
  ```

- run after exit

  ```sh
  docker container start runner-kernel
  docker exec -it runner-kernel /bin/bash
  ```

## Related repos

- https://github.com/initdc/cross-make
- https://github.com/initdc/xuantie-900-gcc
- https://github.com/initdc/yocto-rockchip
- https://github.com/initdc/make-env