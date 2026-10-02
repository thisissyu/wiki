# cuda

## Installation: 手动在家目录下面安装cuda和cudnn

> 场景：
>
> 1. 假设正在跑很多别人的深度学习代码，但是它们分别依赖于不同的cuda和cudnn版本，而如果从系统级别安装，只能固定一个版本，这种情况明显不适合从系统层面安装cuda和cudnn
> 1. 假设在实验室的公用服务器上面，没有管理员权限，而cuda和cudnn的版本是固定的（即管理员从系统层面安装的），也没sudo权限修改，并且就算有，也会修改共用的cuda和cudnn,有可能导致别人没办法跑代码
> 1. 以下方法，考虑到Tensorflow对版本依赖性更强，更易出问题，下面的例子的第一步中是按照Tensorflow来进行的，后面的步骤Pytorch和Tensorflow通用。选择的cuda版本是10.2，对应的cudnn版本是v8.7.0。

1. 确定cuda和cudnn版本。从官网找到版本匹配关系。

   <https://tensorflow.google.cn/install/source?hl=en#tested_build_configurations>

1. 根据上面的版本信息，手动安装cuda

   去cuda官网下载对应安装包。`cuda_10.2.89_440.33.01_linux.run`

   ```bash
   wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda_11.8.0_520.61.05_linux.run
   ```

   在目标安装位置创建上级目录。`mkdir ~/.vocal`

   执行`.run`文件，安装cuda到目标安装位置:

   ```bash
   # 非root安装示例
   sh cuda_10.2.89_440.33.01_linux.run --silent --toolkit --toolkitpath=$HOME/.vocal/cudas/cuda_10.2.89_440.33.01 --defaultroot=$HOME/.vocal/cudas/cuda_10.2.89_440.33.01

   # (1) 如果出现`log file not open`，让管理员用sudo删掉log file，重复上面步骤
   # (2) 如果提示gcc版本不匹配，cuda 10.2依赖gcc 8，gcc7不行。解决如下：
   # 方式1. 让管理员用sudo下面的命令切换系统的全局gcc版本。会影响他人使用
   sudo update-alternatives --config gcc
   # 方式2. 把/usr/bin/gcc-8 链接到~/.vocal/gcc
   ln -s /usr/bin/gcc-8 ~/.vocal/gcc
   export PATH="$HOME/.vocal:$PATH"  # 在`~/.bashrc`文件里面添加
   # 解决gcc问题后，重复执行上面安装cuda步骤

   # 在~/.bashrc文件里,将cuda和cudnn添加环境变量,从而覆盖原先的cuda
   export PATH="$HOME/.vocal/cudas/cuda_10.2.89_440.33.01/bin:$PATH"
   # 把cuda的动态链接库加入环境变量，不然只能调用自己的cuda可执行文件，但是对应的库找不到
   export LD_LIBRARY_PATH="$HOME/.vocal/cudas/cuda_10.2.89_440.33.01/lib64"
   ```

1. 通过第一步确定的cudnn版本，安装对应版本的cudnn

   通过官网安装cudnn。需要注册账号。或者通过下面命令直接下载,注意修改版本

   ```bash
   wget -c https://developer.download.nvidia.com/compute/redist/cudnn/v8.7.0/local_installers/10.2/cudnn-linux-x86_64-8.7.0.84_cuda10-archive.tar.xz

   # 解压tgz文件
   tar xvf 文件名.tgz
   ```

   > 关于下面步骤的解释是：cudnn可以理解为是cuda的一个链接库,只需其路径能让系统发现即可。
   >
   > - 方案1: 把存放cudnn库的路径，加到上面那个LD_LIBRARY_PATH变量里面
   > - 方案2: 直接把cudnn跟cuda放到一起。如下：

   将解压出来的文件夹中的`include`子文件夹和`lib64`子文件夹里的内容，分别移动到上述第二步指定的 cuda 安装路径下面对应的`include`文件夹和`lib64`(或者`lib`)文件夹下面。

   > 下面这一步，如果遇到问题了，再继续进行。如果没遇到问题就不用管:
   >
   > 注意：如果之前 cudnn 的`include`子文件夹里面包含`so.7`结尾的文件(比如`libcudnn.so.7`)，需要将这些移动后的文件位置(对应于`$HOME/.vocal/cudas/cuda_10.2.89_440.33.01/include/libcudnn.so.7`)创建软连接到`$HOME/.vocal/cuda_10.2.89_440.33.01/lib64/libcudnn.so.7`。不然有可能依然报错说找不到`libcudnn.so.7`。也就是说，`libcudnn.so.7`这个需要`include`文件夹和`lib`文件夹都有一份。具体原因没有再研究过了。

   后面如果需要多个版本的cuda和cudnn:
   - 方案1: 只需要改一下`~/.bashrc`里面的`$PATH`和`$LD_LIBRARY_PATH`，再重启一下bash。
   - 方案2: 直接把指定`$PATH`在前面附加成通用的`~/.vocal/cuda`，然后通过软连接的方式，任意把某个cuda版本链接到`~/.vocal/cuda`

## Linux 内核版本更新导致nvidia驱动挂了

> 场景：运行`nvidia-smi`命令的时候，报错：NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.

原因：缺少当前内核的头文件，这通常发生在内核更新之后。

```bash
sudo apt install linux-headers-"$(uname -r)"
```

> Ref: <https://zhuanlan.zhihu.com/p/89714824>

```bash
sudo apt install dkms
# 下面这条命令有可能报错找不到`linux-headers-xxxx-amd64`。此时需要通过apt安装一下。
sudo dkms install -m nvidia -v 418.87.00
```

其中，418.87.00 是之前安装 nvidia 驱动的版本号，可通过下面方法查到：

```bash
# 如果下面这条命令显示的是`nvidia-current-418.87.00`，那还需要将其软连接到`/usr/src/nvidia-418.87.00`
ls /usr/src | grep nvidia
```

## 没有进程，但是显存有占用

用以下命令查看所有在显卡上运行的进程。

**注意: 这种方法会打印所有卡上的进程，因此一旦kill，所有卡的都会停，非常危险，需要谨慎使用**

```bash
fuser -v /dev/nvidia*
```
