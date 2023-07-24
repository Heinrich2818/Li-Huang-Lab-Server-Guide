# 实验室计算服务器使用规则（普通用户 试行版 version 0.1)
## I.实验室计算服务器概况
### i.计算服务器硬件配置
为适应实验室计算以及数据分析方面的需求，本服务器购置于2023年7月，可支持实验室生信分析，机器学习，数据分析等多种任务，目前（至2023年7月）配置如下：

|配件类别|配件型号|配件个数|
|------|------|-------|
|CPU|AMD EPYC 7642 2.3G 48核心 96线程|2|
|主板|原厂RS0A-E1-RS12 AMD主板（含导轨/32个内存插槽/板载双口万兆电口/最大支持4GPU）|1|
|内存|三星RECC DDR4 32G 3200Mbps （共512G）|16|
|GPU|NVIDIA A6000 48G|3|
|系统硬盘|三星 980PRO 2T SSD PCIE4.0|1|
|机械硬盘|西部数据 16TB SATA （共96T，Raid5系统可用48T）|4|
|阵列卡|LSI 9361-8l 2G|1|
|电源|华硕 2400W 1+1 冗余电源|1|
|机箱|华硕 RS720A-E11-RS12 2U机箱 前置3.5寸12盘位，其中8个SATA盘位|1|

### ii.计算服务器软件配置
当前服务器系统为 Linux Ubuntu 22.04，已安装NVIDIA驱动535.54.03，CUDA版本12.2。

为了服务器运行的稳定、机器资源的最大化使用以及适应服务器普通用户的不同需求，本服务器使用LXD轻量虚拟化技术，在服务器（后称宿主机）上建立多个容器，由管理员分配给普通用户使用。普通用户可以根据自己需求，客制化容器配置；根据实际任务需求，管理员可以实时分配服务器资源，最大化机器资源的使用效率。

使用容器的普通用户将不能访问、修改宿主机上配置，以保证宿主机的稳定运行。但LXD作为轻量级的虚拟化技术，会在宿主机与容器间<font color=red>**共享内核**，所以如果用户有涉及内核的操作时，**请联系服务器管理员，谨慎操作。**</font>

### iii.计算服务器默认容器配置
对于普通用户，所分配的容器配置默认如下：

- 系统 Linux Ubuntu 22.04
- NVIDIA驱动535.54.03
- CUDA版本12.2

|资源类别|资源大小|
|-----|---|
|CPU核心|8核 16线程|
|内存（RAM）|16GB|
|硬盘|2T|

- GPU资源的分配较为特殊，<font color=red>**目前所有容器均挂载所有GPU资源，后续根据实际使用情况将会进行调整**</font>
- 如用户任务有较大资源使用需求，请向服务器管理员申请额外资源，任务完成后所分配的资源将会回收，以方便他人使用
- 容器与宿主机均挂载了目录 /share_space，作为容器间公用的存储空间，使用时请不要在共享目录中直接运行、修改文件，请在下载至自己容器空间后再进行操作
- 容器内软件根据用户需求自行安装，conda环境目前推荐安装Miniconda，以节省容器资源

## II.实验室计算服务器使用指南（普通用户）
### i.使用前配置
用户需要安装终端工具（如MobaXterm、WindTerm、XShell等）以登陆服务器，服务器目前暂不支持校外访问，请在校园网下访问服务器
### ii.向管理员申请容器
用户向管理员申请后，将会在宿主机新建容器，并将必要的登录信息分发给用户
### iii.登录容器
在所安装的终端工具上，新建SSH连接，一般需要的登录信息如下：
```
登录IP：xxx.xxx.xxx.xxx(实验室申请的IP)
登陆端口：由管理员分发给用户，端口范围2000-3000
用户名：origin
密码：xxxxxxxx(容器内用户默认密码)
```
### iv.用户账户自定义配置
对于每一个用户，其在自己的容器内都有管理员权限，使用
```
sudo su
```
输入默认密码，获取容器内的管理员权限

```
cd
```

进入容器的 /root 目录，运行命令
```
bash init_lxd.sh
```
根据提示输入自己需要的用户名和密码，完成配置后容器将会重启

### v.完成配置
恭喜你！你已经完成了自己容器的基本配置，现在可以根据自己的需求配置自己的容器了，enjoy it！

### vi.Tips
- <font color=Darkblue> LXD容器支持快照（Snapshot）功能，在进行危险操作前可以联系管理员，备份容器，以供后续恢复</font>

- <font color=Darkblue> 在LXD中如果有使用Docker的需求，需要另外配置安全权限，请联系管理员</font>

- <font color=Darkblue> 实验室服务器目前刚刚上线，预计在使用中会遇到诸多bug，请及时汇报bug</font>
  
## III.部分特殊服务的远程使用方法(更新编辑中)
### i. Jupyter Notebook
#### 1. Jupyter Notebook简介
Jupyter Notebook是基于网页的用于交互计算的应用程序。其可被应用于全过程计算：开发、文档编写、运行代码和展示结果

#### 2. 在服务器端的设置
- 假设用户使用的容器已经配置Miniconda，Jupyter Notebook在所配置的Miniconda环境下进行配置，首先安装Jupyter Notebook
```
conda install jupyter
```
- 生成默认配置文件
```
jupyter notebook --generate-config
```
- 自定义配置文件
```
# Jupyter Notebook的默认打开目录
c.NotebookApp.notebook_dir = '/home/jupyter/documents/jupyter_notebook'
# Jupyter Notebook启动后是否打开浏览器, 设为 False
c.NotebookApp.open_browser = False
# 默认访问端口, 可自己修改
c.NotebookApp.port = 8880
# jupyterlab是否显示quit按钮
c.NotebookApp.quit_button = False
# 如果使用root用户运行,将下列值改为True即可, 运行时也可加参数将其改为True
c.NotebookApp.allow_root = False
```
- 配置完成后启动Jupyter Notebook服务
```
Jupyter Notebook
```
- 在运行后提示的信息中找到token，复制，供本地登录使用
![Jupyter Notebook Token](https://github.com/Heinrich2818/Li-Huang-Lab-Server-Guide/blob/main/manual_pic/server-jn-edit.png "Jupyter Notebook token")
#### 3. 本地设置
本地访问Jupyter Notebook采用SSH隧道穿透方法（也可以采用FRP穿透技术，本文不介绍），首先在本地安装ssh服务

- 打开命令提示符（CMD）
```
ssh -N -f -L localhost:8888:localhost:XXXX -p YYYY remote_user@remote_host
```
其中 `XXXX` 为Jupyter Notebook设置的访问端口，`YYYY` 为容器登陆端口，`remote_user` 为容器内登录用的用户名，`remote_host` 为服务器IP

- 打开浏览器，输入地址：
```
https://localhost:8888/
```
- 在登陆界面粘贴复制的token
  
- 开始使用Jupyter Notebook
![Jupyter Notebook](https://github.com/Heinrich2818/Li-Huang-Lab-Server-Guide/blob/main/manual_pic/jn.png "Jupyter Notebook运行截图")

### ii. CUDA

用户使用的容器中并未安装CUDA，需要使用CUDA直接从宿主机挂载服务

#### 1.容器挂载CUDA
向服务器管理员申请CUDA挂载，服务器管理员将会把CUDA服务挂载至用户容器

#### 2. 选择需要的CUDA版本并挂载
容器进入root
```
sudo su
```
目前支持CUDA版本包含：<font color=red> 12.2，12.1，11.2</font> 

有使用指定版本CUDA需求时，请向管理员申请添加。用户使用如下命令添加软链接，即可使用对应版本的CUDA
```
ln -s /usr/local/cuda-12.2 /usr/local/cuda
```

#### 3. 切换使用的CUDA版本
删除原有软链接
```
unlink /usr/local/cuda
```
重新建立指定版本cuda的软链接（如11.2）
```
ln -s /usr/local/cuda-11.2 /usr/local/cuda
```
