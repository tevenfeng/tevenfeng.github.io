---
title: Baidu Apollo on Jetson TX1 - Trials
date: 2018-02-02 15:28:26
tags: [Apollo, Jetson TX1]
---
# Baidu Apollo on Jetson TX1

&emsp;&emsp;在Jetson TX1上运行Apollo平台。 

&emsp;&emsp;最新版Docker-ce arm64及[Apollo 1.5](https://github.com/tevenfeng/apollo/tree/r1.5.0)。

## 编译内核添加对Docker的支持

&emsp;&emsp;首先，Jetson TX1使用Jetpack 3.1刷完机以后系统为Ubuntu 16.04 arm64。因此，我们需要先对内核打补丁，使得其能够运行Docker（因为Docker是Apollo官方唯一支持的运行环境）。

<!-- more -->

&emsp;&emsp;我们需要自行编译内核，并在编译前打开对docker的相关支持选项。这里我参考了Github上open-horizon的[项目](https://github.com/open-horizon/cogwerx-jetson-tx1/wiki)。因此只需要在终端中执行以下命令即可：

    sudo -s

    JHURL=https://raw.githubusercontent.com/jetsonhacks/buildJetsonTX1Kernel/master

    SRC=$(curl -s $JHURL/scripts/getKernelSources.sh | grep wget | awk '{print $NF}')
    test "$SRC" && echo $SRC || echo "Did not get source URL!"
    mkdir /ssd/src
    curl $SRC | tar jxvOf - sources/kernel_src-tx1.tbz2 | tar jxvpf - -C /ssd/src # ETA: 5 min
    cd /usr/src
    ln -s -f /ssd/src/kernel .
    ln -s -f /ssd/src/hardware .

    curl -s $JHURL/diffs/devfreq/devfreq.patch | patch --verbose /usr/src/kernel/kernel-4.4/drivers/devfreq/Makefile
    curl -s $JHURL/diffs/nvgpu/nvgpu.patch | patch --verbose /usr/src/kernel/nvgpu/drivers/gpu/nvgpu/Makefile
    curl -s $JHURL/diffs/tegra-alt/tegra-alt.patch | patch --verbose /usr/src/kernel/kernel-4.4/sound/soc/tegra-alt/Makefile
    cp -vf /usr/src/kernel/kernel-4.4/drivers/media/platform/tegra/mipical/mipi_cal.h /usr/src/kernel/kernel-4.4/drivers/media/platform/tegra/mipical/vmipi/mipi_cal.h

    curl -s https://raw.githubusercontent.com/open-horizon/cogwerx-jetson-tx1/master/tx1.docker.config.L4T28.1.gz | gunzip | tee /usr/src/kernel/kernel-4.4/.config

    cd /usr/src/kernel/kernel-4.4

    # if you want to make any .config changes type: make menuconfig

    make olddefconfig
    make prepare
    make modules_prepare
    make -j4 Image && make -j4 modules && make modules_install && cp -vf arch/arm64/boot/Image /boot/Image # ETA 12 min

    reboot

&emsp;&emsp;将上述命令完整复制并保存为buildKernel.sh文件。而后执行以下命令：

    sudo -i
    cd {path/to/buildKernel.sh}
    ./buildKernel.sh
    exit

执行完毕及其将会自动重启。

## Apollo开发环境的搭建

&emsp;&emsp;Apollo使用Docker作为开发环境，因此我们需要先安装Docker。

&emsp;&emsp;Apollo项目内包含了安装Docker所需要的脚本，在Apollo项目的根目录下（假设为apollo）执行以下命令即可安装Docker：

    bash scripts/install_docker.sh

&emsp;&emsp;运行apollo-docker镜像（第一次启动会比较慢，需要下载镜像）：

    bash docker/dev_start.sh

&emsp;&emsp;进入apollo-docker镜像开始开发：

    bash docker/dev_into.sh

## 编译运行Apollo

&emsp;&emsp;按照官方文档的描述，上述步骤如果顺利，则可以进入到Apollo-docker环境中，就可以开始进行开发编译工作了。

&emsp;&emsp;官方的demo的编译代码为：

    # To make sure you start clean
    ./apollo.sh clean
    # This will build the full system and requires that you have an nVidia GPU with nVidia drivers loaded
    bash apollo.sh build

&emsp;&emsp;上述命令执行顺利的话，就可以使用以下命令来运行：

    bash scripts/bootstrap.sh

&emsp;&emsp;上述命令执行后，可以打开浏览器进入http://localhost:8888 进入到Apollo Dreamview界面。

&emsp;&emsp;执行下面的命令可以运行demo程序：

    rosbag play -l ./docs/demo_guide/demo.bag

## 我踩过的坑

### /usr/local/bin/bazel: Permission denied

&emsp;&emsp;我在进入到Apollo-docker环境后，执行 bash apollo.sh clean 时报出以下错误：

    /usr/local/bin/bazel: Permission denied

在接下来执行 bash apollo.sh build 时也会报出同样的错误。

&emsp;&emsp;经过检查可以发现实际上这里的bazel是一个静态连接，指向的是同一个目录下的bazel-aarch64文件，而这个文件是没有自行权限的，这就是问题的根源所在。那么就想到用chmod来给它赋权，但是又发现在这个docker镜像中是无法执行sudo命令的，因为百度没有给这个镜像包含sudo包。所以只能通过其他途径了。这就有两个方法。

&emsp;&emsp;其一，直接使用docker cp从百度提供的镜像中将bazel-aarch64这个文件复制一份到本地（host），在本地使用sudo将其赋权以后再docker cp拷贝回百度的这个镜像中去。但是这种方法不推荐，因为实际上百度提供的镜像存在许多小问题，比如ros添加的源使用的是官方的，但是在中国由于众所周知的一些原因这个源在中国是没办法访问的（至少我在天津是这样的，报Hash sum mismatch）。所以还是推荐第二个方法。

&emsp;&emsp;其二（推荐），使用百度提供的dockerfile来重新构建一个镜像，当然这个镜像需要进行相应的修改（如添加sudo包，修改apt的源为中国区，修改ros源为中国区等,附上修改后的dockerfile），再将百度自己构建好的镜像中的bazel执行文件提取出来(docker cp)放入到我们自己编译好的镜像中再为其赋予执行权限即可。

&emsp;&emsp;最开始也想过在生成镜像的时候让它自己生成一个bazel，但是发现在编译完bazel-0.9.0版本后会导致后续出现‘try to mutate a frozen object’的报错。经过Google后得知这是因为项目中使用到了Google的protobuf项目的3.3.0版本，而这个版本在使用bazel构建的过程中生成的.bzl文件格式不能被新版（0.9.0）的bazel识别，从而导致编译出错。而我尝试过编译0.5.4版本的bazel，但是在Jetson TX1板子上编译很容易爆内存死机，所以也没有尝试下去。所以最后索性就从百度自带的镜像中提取出来再赋权也可以运行下去。