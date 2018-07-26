.. figure:: C:\Users\feidi\Documents\BNU\题图.png
   :alt: 题图

   题图
*Land Information System(LIS)*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

LIS 7.2 用户手册
----------------

版本1.8, 2017 年 8 月
^^^^^^^^^^^^^^^^^^^^^

目录
====

1. 介绍
=======

这是土地信息系统（LIS）的用户手册。这个文档讲解了如何下载和安装 7.2 版
LIS
的代码和其所需要的数据。本文讲解了如何构建和运行代码，另外在最后还提到了如何下载用于验证的输出数据。

本文分为12个章节，分别如下：

1.  **介绍**\ ：目前你在读的章节

2.  **背景**\ ：LIS 项目的背景

3.  **基础信息**\ ：一般信息，步骤，指导和后文所用到的定义

4.  **获取源代码**\ ：下载源码所需步骤

5.  **构建可执行文件**\ ：构建可执行文件所需步骤

6.  **运行可执行文件**\ ：准备和提交运行所需步骤，还提到了多种运行时配置

7.  **测试案例**\ ：描述了 LIS 的测试案例

8.  **输出数据处理**: 后处理输出数据进行可视化的所需步骤

9.  **LIS配置文件**\ ：描述了用户配置选项

10. **输入驱动变量的规范**\ ：描述了用户可配置的输入驱动变量选项

11. **模型输出规范**\ ：描述了用户可配置的输出变量选项

12. **用户支持**\ ：描述如何向LIS开发团队请求帮助并向其提供反馈。

1.1 更新记录 --- 略
-------------------

2. 背景
=======

*本章提供了一些有关 LIS 的基本信息*

2.1. LIS
--------

土地信息系统（LIS）是一个灵活的地表建模和数据同化框架，其目标是整合基于卫星和地面的观测数据产品和先进的地表建模技术，以产生最佳的地表状态和通量场。
LIS基础设施提供建模工具，将这些观测结果与模型预测结合起来，以 1km
和更精细的空间分辨率以及1小时和更精细的时间分辨率生成对土地表面条件（如土壤湿度，蒸发，积雪和径流）的改进估计。
LIS的精细空间建模能力允许它以其完全原始分辨率利用 EOS
时代的观测，例如MODIS 叶面积指数，积雪覆盖和表面温度。 LIS
具有高性能和灵活的设计，为数据集成和同化提供基础设施，并在一组陆地表面模型（LSM）上运行，以便在用户指定的区域或全局域上进行扩展。
LIS采用先进的软件工程原理设计，可实现建模工具，数据资源和同化算法的重用和社区共享。该系统被设计为面向对象的框架，其定义的抽象定制和扩展到不同的应用程序。这些可扩展的接口允许结合新的域，LSM，地表参数，气象输入，数据同化和优化算法。这些接口的可扩展性和系统的组件样式规范允许快速原型设计和新应用程序的开发。这些功能使
LIS
既可以作为水文研究的问题解决环境（PSE），实现准确的全球水和能源循环预测，也可以作为决策支持系统（DSS）为灾害管理，水资源管理，农业管理，数值天气预报，空气质量和军事流动性评估等应用领域
生成有用的信息。

LIS
目前包括一套全面的子系统，用于支持非耦合和耦合的陆地数据同化。具有相关子系统的
LIS 框架示意图如下图所示。 LIS-LSM
子系统是LIS的核心，通过一系列社区地表模型和输入数据支持高性能，可互操作和便携式陆地表面建模。此外，LIS-LSM
子系统设计用于封装地球系统模型的陆地表面组件。 LIS-WRF子系统通过与 WRF
大气模型的单向和双向耦合支持耦合陆地 -
大气模拟，从而产生水文气象模拟能力，可用于评估地表过程对水文预测的影响。数据同化（LIS-DA）子系统支持多种数据同化算法，这些算法专注于生成改进的水文模型状态估计。最后，优化（LIS-OPT）子系统在LIS中支持一套高级优化和不确定性建模工具。

2.2. LIS 核心
-------------

LIS 软件系统的核心部分是控制程序执行的 LIS 核心。 LIS
核心是一个模型控制和输入/输出系统（由许多子程序，用 Fortran
90源代码编写的模块组成），可驱动多个离线一维 LSM。诸如 CLM 和 Noah
的一维 LSM 应用土壤 - 植被 -
积雪介质的物理过程的控制方程。这些陆地表面模型旨在表征植被表面和大气之间的质量，能量和动量的转移。当像元内存在多种植被类型时，栅格进一步划分为“瓦片”，每个瓦片表示网格框内的特定植被类型，以模拟亚像元尺度的变化性。

LIS
核心的执行从读取用户规定开始，包括建模域，空间分辨率，运行持续时间等。\ **运行可执行文件**\ 描述了用户指定的详尽参数列表。接下来是模型参数的读取和计算。时间循环开始并读取驱动数据，计算时间/空间插值并根据需要进行修改。驱动数据用于指定地表模型的边界条件。
LIS核心应用时间/空间插值将驱动数据转换为模型所需的适当分辨率。为“瓦片”向量运行所选模型，并以指定的输出间隔写入输出和重新启动文件。

LIS核心提供的一些显着特征包括：

-  基于植被类型的“瓦片”或“patch”方法来模拟子网格尺度的可变性。
-  利用各种卫星和地面观测系统。
-  从现有地形，植被和土壤覆盖中获取模型参数。
-  可扩展的接口，便于整合新的地表模型，驱动方案。
-  使用模块化，面向对象的样式设计，允许用户在构建可执行文件时仅选择感兴趣的组件，从而允许“即插即用”不同功能。
-  能够执行区域建模（仅在感兴趣的领域）。
-  提供多种可扩展的并行处理操作模式。

有关LIS内核设计的详细说明，请参阅软件设计文档。
LIS参考手册提供了LIS中可扩展接口的描述。本文档描述了不同组件的“即插即用”功能。

3. 基础信息
===========

*本节提供了一些基础信息，使您可以更轻松地阅读本指南。*

3.1. 命令
---------

命令以固定宽度字体编写。例如：

.. code:: shell

    % cd /path/to/LISv7.0

.. code:: shell

    %ls

“… compiler flags, then run :math:`gmake`.”

注：\ :math:`\% ` 代表命令行提示符。输入任何命令时都无需输入。

3.2. 文件名
-----------

文件名以斜体书写。例如：

*/ path/to/LISv7.0/src*

4. 获取源代码
=============

本节介绍如何获取构建LIS可执行文件所需的源代码。

LIS 公开发布7.1rp1 开始，LIS 源代码在 NASA
开源协议（NOSA）下作为开源提供。请访问 `LIS
的网站 <http://lis.gsfc.nasa.gov/>`__\ 获取 NOSA 的副本。

由于 LIS 开发的历史，LIS
源代码的早期版本可能无法自由分发。旧的源代码仅适用于美国政府机构或拥有美国政府拨款/合同的实体。
`LIS
的网站 <http://lis.gsfc.nasa.gov/>`__\ 解释了符合相关条件的人员如何索取旧源代码的副本。

4.1. 关于文件系统的重要说明
---------------------------

LIS是在 Linux / Unix
平台上开发的。它的构建过程需要区分大小写的文件系统。请确保将 LIS
代码解压缩和/或检出到区分大小写的文件系统中的目录中。特别是，如果您在Windows
或 Macintosh 系统上托管的基于 Linux 的虚拟机中使用 LIS，请将 LIS
源代码移动到虚拟机中的目录中，不要在共享文件夹中编译/运行 LIS。。

4.2. 获取公开发布代码
---------------------

LIS 7.2源代码可以从 LIS
的网站下载为tar文件。我们鼓励所有用户填写注册表并加入邮件列表，这两种也可以从LIS的网站访问。下载LIS
tar文件后：

**第一步**

创建一个放置 tar 文件解压后文件的目录。我们称该目录为 *TOPLEVELDIR*\ 。

**第二步**

将 tar 文件移动到该目录下。

.. code:: shell

    % mv LIS_public_release_7.2r.tar.gz TOPLEVELDIR

**第三步**

转到该目录。

.. code:: shell

    % cd TOPLEVELDIR

**第四步**

解压 tar 文件

.. code:: shell

    % gzip -dc LIS_public_release_7.2r.tar.gz | tar xf -

**注**

请注意，在本文档的其余部分中，包含 LIS
源代码的目录将被称为\ *$WORKING*\ 。

4.3. 受限代码获取（仅针对 NASA）---略
-------------------------------------

4.4. 源文件
-----------

检查 LIS 源代码（根据获取源代码部分中的说明）将创建一个名为 src 的目录。
src 结构如下：

-  *LICENSE* 证书

-  *arch* 设置选项

-  *config* 配置样例

-  *core* 核心

-  *dataassim* 数据同化
-  *algorithm* 算法

   -  di
   -  enkf
   -  enkfgrace

-  *obs*

   -  ANSA\_SCF
   -  ANSA\_SNWD
   -  ESACCI\_sm
   -  GCOMW\_AMSR2L3SND
   -  GCOMW\_AMSR2L3sm
   -  GRACE
   -  LPRM\_AMSREsm
   -  MODISsca
   -  NASA\_AMSREsm
   -  NASA\_SMAPsm
   -  PMW\_snow
   -  SMMR\_SNWD
   -  SMOPSsm
   -  SMOS\_L2sm
   -  SMOS\_NESDIS
   -  SSMI\_SNWD
   -  WindSat\_sm
   -  pildas

-  perturb

   -  gmaopert

-  *docs* 文档

-  *forcast* 预测

-  算法

-  *interp* 插值

-  *irrigation* 灌溉
-  drip 滴灌
-  flood 漫灌
-  sprinkler 喷灌

-  *lib*
-  lis-cnem3
-  lis-crtm
-  lis-crtm-profile-utility

-  *make*

-  *metforcing* 气象驱动
-  3B42RT

-  3B42RTV7

-  3B42V6

-  3B42V7

-  AWAP

-  Bondville

-  PALSmetdata

-  PILDAS

-  RFE2Daily

-  RFE2gdas

-  WRFout

-  agrrad

-  agrradps

-  ceop

-  chirps2

-  climatology

-  cmap

-  cmorph

-  coop

-  ecmwf

-  ecmwfreanal

-  gdas

-  gdasLSWG

-  gdasT1534

-  genMetForc

-  geos

-  geos5fcst

-  gfs

-  gldas

-  gswp1

-  gswp1

-  imerg

-  *offline* 离线

包含离线操作模式的主程序

-  *optUE*

用于优化支持的顶级目录，包括以下子组件

-  params

   包含以下优化算法实现的目录

   -  DEMC

   -  DEMCz

   -  GA

   -  LM

   -  MCSIM

   -  RWMCMC

-  类型

   -  paramestim
   -  objfunc

      -  LL
      -  LS
      -  P

   -  obs

      -  AMSRE\_SR
      -  EmptyObs
      -  LPRM\_AMSREsm
      -  template

-  *params*

包含以下地表模型参数的实现的目录

-  gfrac
-  lai

-  *pligins* 拓展功能

定义可扩展功能的功能表注册表的模块

-  *routing* 进程模型

-  *rtms* 辐射转化

-  *runmodes* 运算模型

-  *surfacemodels* 表面模型

-  *testcases* 测试案例

-  *utils* 杂项实用工具 源代码文档可以在 LIS的网站
   上找到。按照“文档”链接

+--------------------------------------------------------------------------+
| #5. 建立可执行文件 本节介绍如何构建源代码并创建LIS的可执行文件：命名为   |
| LIS。 有关使用大小写敏感文件系统编译/运行 LIS                            |
| 的信息，请参阅“关于文件系统的重要说明”一节。 ## 5.1. 开发工具            |
| 此代码已在Linux PC（基于Intel /                                          |
| AMD）系统和Cray系统上编译和运行。这些说明希望您使用这样的系统。特别需要： |
|                                                                          |
| ### 5.1.1. Linux \* 编译器 \* 英特尔 Fortran 编译器版本 14 或 15         |
| 与相应的英特尔 C 编译器 \* 或GNU的编译器集合4.9.2，gfortran和gcc。 \*    |
| 工具 \* GNU的 make，gmake，版本3.77或3.81 \* perl, 版本5.10 \*           |
| python，版本2.6或2.7 ###5.1.2. Cray/Linux \* 编译器 \* 英特尔 Fortran    |
| 编译器版本 14 或 15 与相应的英特尔 C 编译器 \* 工具 \* GNU的             |
| make，gmake，版本3.77或3.81 \* perl, 版本5.10 \* python，版本2.6或2.7 ## |
| 5.2. 所需软件库 要构建 LIS 执行文件，必须在系统上安装以下库： ### 5.2.1. |
| Earth System Modeling Framework (ESMF) version 5.2.0rp3 (or higher)      |
| 地球系统建模框架                                                         |
| （http://www.earthsystemmodeling.org/download/releases.shtml）           |
| 有关安装ESMF（支持MPI或不支持MPI（“mpiuni”））的详细信息，请阅读ESMF用户指南。 |
|                                                                          |
| 请注意，从ESMF版本5开始，ESMF开发团队正在尝试保持与其后续版本的向后兼容性。 |
|                                                                          |
| ### 5.2.2. JasPer version 1.900.1 (or higher)                            |
| (http://www.ece.uvic.ca/~frodo/jasper/) 请注意，在运行 ``configure``     |
| 命令时，必须包含 ``--enable-shared`` 选项。 ### 5.2.3. GRIB-API version  |
| 1.12.3 (or higher) (https://software.ecmwf.int/wiki/display/GRIB/Home)   |
| ###5.2.4. NetCDF either version 3.6.3 or version 4.3.0 (or higher)       |
| (http://www.unidata.ucar.edu/software/netcdf) 有关安装 NetCDF            |
| 的详细信息，请阅读在线文档。 **关于NetCDF 4特别需要注意的是:**           |
| 您还必须选择是否在启用压缩的情况下进行编译。使用压缩进行编译需要HDF      |
| 5和zlib库。要启用压缩，请将 ``--enable-netcdf-4`` 添加到 ``configure``   |
| 选项。要禁用压缩，请在 ``configure``                                     |
| 选项中添加\ ``--disable-netcdf-4``\ 。 无压缩安装 NetCDF 4 的示例：      |
+==========================================================================+
| Setting up configuration for LIS version 7.2r... Parallelism (0-serial,  |
| 1-dmpar, default=1): Optimization level (-2=strict checks, -1=debug,     |
| 0,1,2,3, default=2): Assume little/big\_endian data format (1-little,    |
| 2-big, default=2): Use GRIBAPI? (1-yes, 0-no, default=1): Enable         |
| AFWA-specific grib configuration settings? (1-yes, 0-no, default=0): Use |
| NETCDF? (1-yes, 0-no, default=1): NETCDF version (3 or 4, default=4):    |
| NETCDF use shuffle filter? (1-yes, 0-no, default = 1): NETCDF use        |
| deflate filter? (1-yes, 0-no, default = 1): NETCDF use deflate level? (1 |
| to 9-yes, 0-no, default = 9): Use HDF4? (1-yes, 0-no, default=1): Use    |
| HDF5? (1-yes, 0-no, default=1): Use HDFEOS? (1-yes, 0-no, default=1):    |
| Use MINPACK? (1-yes, 0-no, default=0): Use LIS-CRTM? (1-yes, 0-no,       |
| default=0): Use LIS-CMEM? (1-yes, 0-no, default=0): Use LIS-LAPACK?      |
| (1-yes, 0-no, default=0):                                                |
+--------------------------------------------------------------------------+

 configure.lis file generated successfully
------------------------------------------

Settings are written to configure.lis in the make directory. If you wish
to change settings, please edit that file. Processing plugins...
Settings are written to Filepath and LIS\_plugins.h in the make
directory. If you wish to change settings, please create/modify your
make/user.cfg file. Please see configs/user.cfg for more information. To
compile, run the compile script.
------------------------------------------------------------------------
\`\`\`

在每个提示命令时，选择所需的值（0 或
1）。如果您需要默认值，则只需按Enter键即可。

大多数配置选项都是不言自明的。以下是一些具体说明：

-  对于 ``Parallelism (0-serial, 1-dmpar, default=1):``, dmpar 表示启用
   MPI
-  对于
   ``Assume little/big_endian data format (1-little, 2-big, default=2):``,选择默认值2.默认情况下，LIS
   以大端格式读取和写入二进制数据。如果已将所有必需的二进制数据重新格式化为小端格式，则仅选择值1。
   `关于字节序-大端与小端 <https://blog.csdn.net/jasonchen_gbd/article/details/78671550>`__
-  对于\ ``Use GRIBAPI? (1-yes, 0-no, default=1):``,
   选择默认值1。从技术上讲，LIS
   不需要GRIB支持;但是，大多数常用驱动数据都在 GRIB 中，这使得 GRIB
   支持成为一个实际需求。
-  对于 ``Use LIS-CRTM? (1-yes, 0-no, default=0):``, 如果您希望启用
   LIS-CRTM2EM 支持，则还必须启用 LIS-CMEM 支持。所以对于
   ``Use LIS-CMEM? (1-yes, 0-no, default=0):``,需要选择 1。
-  对于\ ``Use LIS-CMEM? (1-yes, 0-no, default=0):``, 如果您希望启用
   LIS-CMEM 支持，则还必须启用 LIS-CRTM。所以对于
   ``Use LIS-CRTM? (1-yes, 0-no, default=0):``,需要选择 1。

请注意，由于涉及 NetCDF 3 和 HDF 4
库中的多个定义的问题，您无法编译同时支持 NetCDF 3 和 HDF 4 的 LIS。

请注意，如果您在没有压缩的情况下编译 NetCDF 4，那么在指定
NETCDF版本（3或4，默认= 4）时，选择3.然后您必须手动将 ``-lnetcdff``
附加到 *make / configure.lis*\ n 文件中的\ ``LDFLAGS`` 变量

**第五步**

通过运行编译脚本编译LIS源代码。

.. code:: shell

    % ./compile

该脚本将编译随 LIS 提供的库，依赖关系生成器以及 LIS
源代码。成功完成编译脚本后，可执行文件 LIS 将放在 *$WORKING* 目录中。

**第六步**

最后，将 LIS可执行文件复制到你的运行目录 *$RUNNING* 下。

5.4.1. 通过 LIS 插件自定义 build --- 暂略
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

LIS中的各种组件被认为是插件，这意味着它们是可选的，可以在编译时启用/禁用。默认情况下，大多数插件都已启用，除了
1）受限制的组件（在LIS的公共版本中不可用），2）仍处于开发阶段的组件，3）默认情况下禁用旧的/不支持的组件。如果您希望使用其默认插件配置编译LIS，则只需按照上述六个步骤操作即可。您可以跳过本节的其余部分。如果您希望切换是否启用/禁用插件，则必须创建
user.cfg 文件。

5.5. 生成文档
-------------

LIS代码中的文档使用ProTex
http://gmao.gsfc.nasa.gov/software/protex/文档系统。可以使用
*$WORKING/utils* 目录中的 doc.sh 生成 LaTeX
格式的文档。此命令生成文档，生成许多 LaTeX 文件。可以使用 pdflatex
等实用程序轻松地将这些文件转换为pdf。

6. 运行可执行文件
=================

本节介绍如何运行LIS可执行文件。

首先，应该创建一个目录来运行 LIS。建议您在与源代码分开的目录中运行
LIS。该运行目录应称为 *$RUNNING*\ 。接下来，将 LIS
可执行文件复制到正在运行的目录中。

.. code:: shell

    % cp $WORKING/LIS $RUNNING

``LIS`` 的单进程版本由 *$RUNNING* 目录中发出的以下命令执行

.. code:: shell

    % ./LIS

请注意，使用Lahey Fortran编译器时，必须发出此命令以运行LIS的单进程版本：

.. code:: shell

    % ./LIS -Wl,T

``LIS`` 的并行版本必须通过 ``mpirun``
脚本或类似机制运行。假设正确安装了MPI，LIS 模拟将通过 *$RUNNING*
目录中发出的以下命令执行。

.. code:: shell

    % mpirun -np N ./LIS

``-np N`` 标志指示在运行中使用的进程数，其中将 N
替换为要使用的进程数。在多处理器机器上，可以使用此标志利用 LIS
的并行处理能力。

某些系统要求您将作业提交到批处理队列。有关如何执行此操作的说明，请咨询系统管理员。

请注意，在运行LIS之前，必须将环境设置为具有无限的堆栈大小。对于 Bash
shell，运行

.. code:: shell

    % ulimit -s unlimited

要自定义运行，必须修改lis.config配置文件。有关更多信息，请参见LIS配置文件一节。

6.1 命令行参数
--------------

``LIS [-f  | --file ] -f , --file``

指定lis运行时配置文件的名称。

默认情况下，LIS 运行时配置选项在名为 *lis.config*
的文件中定义。使用此命令行参数指定备用运行时配置文件。

7. 测试用例
===========

本节介绍如何获取以及如何使用 LIS
团队提供的测试用例。有两类测试用例：公共测试和内部测试。

7.1. 公共测试
-------------

这些测试用例是为一般 LIS 用户提供的。它们有助于演示 LIS
和其所依赖库的安装，还演示了如何配置 LIS
的几个不同用例。这些测试用例由三部分组成：一个测试用例的子目录包括LIS
源代码，输入数据和输出数据。

7.1.1. 测试用例子目录
~~~~~~~~~~~~~~~~~~~~~

公共测试用例包含在 *src/testcases/public* 目录中。可用的测试是：

**NLDAS2-a**

​ NLDAS domain + NLDAS-2 forcing + Noah-3.3 LSM + HYMAP router + AMSR-E
DA soil moisture

**NLDAS2-b**

​ NLDAS domain + NLDAS-2 forcing + CLSM-F2.5 LSM + HYMAP router + GRACE
DA

**NLDAS2-c**

​ RDHM 3.5.6 (Sac-HTET) over the NLDAS domain using NLDAS-2 forcing

**GLDAS**

​ VIC 4.1.2.l over a global 1 deg domain using Princeton forcing

**pmw\_snowda\_nam**

​ PMW SNOW DA case over Alaska with NAM forcing

**OPTUE**

​ PMM related case featuring parameter and uncertainty estimation

**geowrsi.2**

​ GeoWRSI over an East Africa domain using USGS PET and RFE2 precip

这些测试用例子目录包含帮助您测试LIS所需的文件。特别的，它们包含所需的运行时配置文件，并且包含用于下载数据的脚本。每个测试用例子目录都包含一个
*README* 文件。每个 *README*
文件都包含测试用例的说明以及有关如何下载和运行测试用例的说明。

7.1.2. 输入和输出数据
~~~~~~~~~~~~~~~~~~~~~

运行公共测试用例所需的输入和输出数据托管在 LIS
的公共数据门户上。请参阅LIS'网站（http://lis.gsfc.nasa.gov）的“LIS测试用例”部分。

7.2. 内部测试
-------------

这些测试用例通常由 LIS 开发团队用于测试 LIS
源代码的各种组件。这些测试用例由三部分组成：此类测试用例子目录中包含 LIS
源代码，输入数据和输出数据。

7.2.1. 测试用例子目录
~~~~~~~~~~~~~~~~~~~~~

testcases子目录的布局与顶级src目录的布局相匹配。例如，LIS 包含对处理
GDAS 驱动数据的支持。这些例程在 *src/metforcing/gdas* 中。 GDAS
的测试用例位于\ *src/testcases / metforcing/gdas* 中。

这些测试用例子目录包含几个文件来帮助您测试
LIS。例如，\ *src/testcases/metforcing/gdas* 测试用例包含六个文件：

1. README

包含如何运行测试用例的说明

2. ldt.config

LDT 处理测试用例输入参数的配置文件

3. lis.config

测试用例的配置文件

4. MODEL\_OUTPUT\_LIST.TBL

测试用例输出的测试文件

5. output.ctl

GrAD的描述符文件。此文件与GrADS一起用于绘制运行LIS时将生成的输出数据。您还可以阅读此文件以获取有关输出文件结构的元数据。此元数据有助于您使用其他程序绘制输出。

6. testcase.ctl 是一个GrADS描述符文件。此文件与 GrADS 一起用于绘制通过
   LIS 网站（http://lis.gsfc.nasa.gov）分发的输出数据，用于此测试用例 。
   ###7.2.2. 用例 LDT

对于每个测试用例，LIS团队创建了一个相应的 LDT 输入数据文件，其中包含运行
LDT 以生成 LIS 输入参数文件所需的所有数据。

要获取测试用例的LDT输入数据：

1. 访问LIS 的网站：http://lis.gsfc.nasa.gov
2. 访问“LIS 测试用例”链接。
3. 访问所需测试用例的链接。

7.2.3. 测试用例输入
~~~~~~~~~~~~~~~~~~~

对于每个测试用例，LIS团队创建了一个相应的输入数据文件，其中包含运行 LDT
以生成 LIS 输入参数文件所需的所有数据。

要获取测试用例的LDT输入数据：

1. 访问LIS 的网站：http://lis.gsfc.nasa.gov
2. 访问“LIS 测试用例”链接。
3. 访问所需测试用例的链接。

7.2.4. 测试用例输出
~~~~~~~~~~~~~~~~~~~

对于每个测试用例，LIS
团队创建了一个相应的输出数据文件，其中包含从测试用例生成的所有输出数据。

为了获得测试用例的输出数据：

1. 访问LIS 的网站：http://lis.gsfc.nasa.gov
2. 访问“LIS 测试用例”链接。
3. 访问所需测试用例的链接。

7.2.5. 输出例子
~~~~~~~~~~~~~~~

例如，“Noah 3.3 LSM TEST”的输出数据包含：

    *• TARGET\_OUTPUT/lislog.0000 •
    TARGET\_OUTPUT/SURFACEMODEL.d01.stats
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210290300.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210290600.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210290900.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210291200.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210291500.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210291800.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210292100.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210300000.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_RST\_NOAH33\_200210300000.d01.nc
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210300300.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210300600.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210300900.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210301200.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210301500.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210301800.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_HIST\_200210302100.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021031/LIS\_HIST\_200210310000.d01.gs4r
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021031/LIS\_RST\_NOAH33\_200210310000.d01.nc
    •TARGET\_OUTPUT/SURFACEMODEL/2002/20021031/LIS\_RST\_NOAH33\_200210310100.d01.nc*

*TARGET\_OUTPUT/lislog.0000* 是运行的日志。

*TARGET\_OUTPUT/SURFACEMODEL.d01.stats* 包含运行的统计数据。

与
*TARGET\_OUTPUT/SURFACEMODEL/2002/20021029/LIS\_HIST\_200210290300.d01.gs4r*
有同样后缀的文件包含运行的结果。阅读 LIS 源代码测试用例子目录下的
*testcase.ctl* 文件

与\ *TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_RST\_NOAH33\_200210300000.d01.nc*
有同样后缀名的文件时重启文档。当继续或重启新运算时可能会用到。数据对文件名中日期戳指示的日期和时间有效。
例如，\ *TARGET\_OUTPUT/SURFACEMODEL/2002/20021030/LIS\_RST\_NOAH33\_200210300000.d01.nc*
对 2002-10-30T00:00:00 有效。

这些输出数据都很大，在读取之前需要预处理，请参考 输出数据处理

8. 输出数据处理
===============

本节介绍如何以各种格式处理生成的输出。生成的输出可以用 Fortran
二进制，GRIB 或 NetCDF 格式编写。有关详细信息，请参见运行时选项。
运行时选项

通过运行 LIS 可执行文件创建的输出数据集将写入 *RUNNING/
OUTPUT/SURFACEMODEL/*
目录的子目录中。请注意，\ *RUNNING/OUTPUT/SURFACEMODEL/*
是在运行时创建的，该 OUTPUT 是用户可配置的名称。请参见 运行时选项
。输出数据由ASCII文本文件和某种二进制格式的模型输出组成。

例如，如果执行Noah 3.3测试用例。

该运行将会创建一个 *$RUNNING/OUTPUT/ directory* 目录。该目录包含

+--------------------------+----------------------+
| 文件名                   | 概要                 |
+==========================+======================+
| SURFACEMODEL.d01.stats   | 输出统计汇总         |
+--------------------------+----------------------+
| SURFACEMODEL             | 包含输出数据的目录   |
+--------------------------+----------------------+

*SUPERMODEL* 的目录包含 *YYYY/YYYYMMDD*
形式的子目录，（年/年月日），都与仿真的运行日期相一致。

比如， *SUPERMODEL* 包含 2002/20021030 子目录。

它包含可执行文件产生的四种输出文件。它们是：

-  LIS\_HIST\_200210300000.d01.gs4r
-  LIS\_HIST\_200210300300.d01.gs4r
-  LIS\_HIST\_200210300600.d01.gs4r
-  LIS\_HIST\_200210300900.d01.gs4r
-  LIS\_HIST\_200210301200.d01.gs4r
-  LIS\_HIST\_200210301500.d01.gs4r
-  LIS\_HIST\_200210301800.d01.gs4r
-  LIS\_HIST\_200210302100.d01.gs4r

请注意，每个文件名都包含一个日期戳，用于标记数据对应的年，月，日，小时和分钟。其他陆地表面模型的输出数据文件类似。这里
gs4r 扩展名对应于 Fortran
二进制输出格式。其他二进制格式的输出数据文件类似。

8.1. Fortran 二进制输出格式
---------------------------

对于 Fortran 二进制格式，LIS在顺序访问模式下将输出数据写为4字节 REAL。

写入变量的顺序与统计摘要文件中的顺序相同;例如，\ *SURFACEMODEL.d01.stats*\ 。

生成的输出可以以二维网格格式或一维向量写入。有关详细信息，请参见
运行时选项
。如果写为一维向量，则必须先将输出转换为二维网格，然后才能将其显示。这留给读者练习。

8.2. GRIB 输出格式
------------------

GRIB1是一种自描述数据格式。可以使用实用程序
``wgrib``\ （http://www.cpc.ncep.noaa.gov/products/wesley/wgrib.html）或实用程序\ ``grib_dump``\ （随GRIB-API提供;请参阅）检查GRIB1中生成的输出文件;请参阅部分所需的软件库）。

8.3. NETCDF 输出形式
--------------------

NetCDF是一种自描述格式。可以使用实用程序
``ncdump``\ （随NetCDF提供;请参阅所需软件库
）检查NetCDF中生成的输出文件。

9. LIS 配置文件
===============

本节介绍了 *lis.cofig* 文件中的选项。

9.1. 总体驱动选项
-----------------

``Running mode``: 规定了 LIS 所用的运行模式。可选值有：

+--------------------------+----------------+
| 值                       | 描述           |
+==========================+================+
| “retrospective”          | 传统模式       |
+--------------------------+----------------+
| “WRF coupling”           | 耦合WRF模式    |
+--------------------------+----------------+
| “parameter estimation”   | 参数估计模式   |
+--------------------------+----------------+
| “ensemble smoother”      | 集合平滑？     |
+--------------------------+----------------+
| “forecast”               | 预测仿真模式   |
+--------------------------+----------------+

*lis.config* 输入示例

.. code:: shell

    Running mode:retrospctive

``Number of nests``
:指定用于运行的嵌套数。值1或更高是可以接受的。最大嵌套数受系统可用内存量的限制。使用空格作为分隔符来完成不同嵌套的规范。请参阅下面的进一步说明。请注意，所有嵌套域应在相同的投影和相同的地表模型上运行。

*lis.config* 输入示例

.. code:: shell

    Number of nests: 1

``Number of surface model types``:指定用于运行的曲面模型类型的数量。值1到``LIS_rc％max_model_types``\ （当前等于3）是可以接受的。

*lis.config* 输入示例

.. code:: shell

    Number of surface model types: 1

``Surface model types``:指定用于运行的表面模型类型。可接受的值是：

+-----------+---------------+
| Value     | Description   |
+===========+===============+
| LSM       | 地表模型      |
+-----------+---------------+
| Lake      | 湖泊模型      |
+-----------+---------------+
| Glacier   | 冰川模型      |
+-----------+---------------+

*lis.config* 输入示例

.. code:: shell

    Surface model types: LSM

``Surface model output interval``: 指定表面模型输出间隔

参考定义时间间隔 关于如何指定时间间隔的表述。

*lis.config* 输入示例

.. code:: shell

    Surface model output interval: 3hr

``Land surface model``:指定要运行的地面表面模型。可选值如下：

+---------------+-------------------------------------+
| Value         | Description                         |
+===============+=====================================+
| none          | template lsm                        |
+---------------+-------------------------------------+
| Noah.2.7.1    | Noah version 2.7.1                  |
+---------------+-------------------------------------+
| Noah.3.2      | Noah version 3.2                    |
+---------------+-------------------------------------+
| Noah.3.3      | Noah version 3.3                    |
+---------------+-------------------------------------+
| Noah.3.6      | Noah version 3.6                    |
+---------------+-------------------------------------+
| NoahMP.3.6    | Noah-MP version 3.6                 |
+---------------+-------------------------------------+
| RUC.3.7       | RUC version 3.7                     |
+---------------+-------------------------------------+
| CLM.2         | CLM version 2.0                     |
+---------------+-------------------------------------+
| VIC.4.1.1     | VIC version 4.1.1                   |
+---------------+-------------------------------------+
| VIC.4.1.2     | VIC version 4.1.2.l                 |
+---------------+-------------------------------------+
| Mosaic        | Mosaic                              |
+---------------+-------------------------------------+
| HySSiB        | Hy-SSiB                             |
+---------------+-------------------------------------+
| GeoWRSI.2     | GeoWRSI version 2.0                 |
+---------------+-------------------------------------+
| CABLE.1.4b    | CABLE version 1.4b                  |
+---------------+-------------------------------------+
| “CLSM F2.5”   | Catchment Fortuna-2\_5              |
+---------------+-------------------------------------+
| RDHM.3.5.6    | RDHM 3.5.6 (SAC-HTET and SNOW-17)   |
+---------------+-------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Land surface model: Noah.2.7.1

``Open water model``\ ：指定要运行的开放水域模型。可选值有：

+-------------------------+--------------------+
| 值                      | 描述               |
+=========================+====================+
| “template open water”   | 模板开放水域模型   |
+-------------------------+--------------------+

*lis.config* 输入示例

.. code:: shell

    Open water model:

``Number of met(Meteorological) forcing sources`` :指定使用的
气象驱动数据库的数量。可选值为0或更高。

*lis.config* 输入示例

.. code:: shell

    Number of met forcing sources: 1

``Met forcing chosen ensemble member``:指定来自给定驱动数据源的所需集合成员在所有LIS集合成员中分配。仅当驱动数据源包含其自己的集合时，才会启用此选项。

*lis.config* 输入示例

.. code:: shell

    Met forcing chosen ensemble member:

``Blending method for forcings``:
指定在使用一个或多个驱动数据集时的混合方法。可选值是：

+------------+----------------------------------------------------------------------+
| 值         | 描述                                                                 |
+============+======================================================================+
| overlay    | 数据集按照指定的顺序依次覆盖。当仅使用一个数据集的时候选择该方法。   |
+------------+----------------------------------------------------------------------+
| ensemble   | 每个强制数据集都分配给一个单独的整体成员。                           |
+------------+----------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Blending method for forcings: overlay

``met驱动源``\ ： 指定运行的 met
驱动数据源。应以列格式指定值。可选的来源值有：

+-------------------------------+--------------------------------------+
| 值                            | 描述                                 |
+===============================+======================================+
| “none”                        | None                                 |
+-------------------------------+--------------------------------------+
| “GDAS”                        | GDAS                                 |
+-------------------------------+--------------------------------------+
| “GEOS”                        | GEOS                                 |
+-------------------------------+--------------------------------------+
| “GEOS5 forecast”              | GEOS5 Forecast                       |
+-------------------------------+--------------------------------------+
| “ECMWF”                       | ECMWF                                |
+-------------------------------+--------------------------------------+
| “GSWP1”                       | GSWP1                                |
+-------------------------------+--------------------------------------+
| “GSWP2”                       | GSWP2                                |
+-------------------------------+--------------------------------------+
| “ECMWF reanalysis”            | ECMWF Reanalysis                     |
+-------------------------------+--------------------------------------+
| “PRINCETON”                   | Princeton                            |
+-------------------------------+--------------------------------------+
| “NLDAS1”                      | NLDAS1                               |
+-------------------------------+--------------------------------------+
| “NLDAS2”                      | NLDAS2                               |
+-------------------------------+--------------------------------------+
| “GLDAS”                       | GLDAS                                |
+-------------------------------+--------------------------------------+
| “GFS”                         | GFS                                  |
+-------------------------------+--------------------------------------+
| “MERRA-Land”                  | MERRA-Land                           |
+-------------------------------+--------------------------------------+
| “MERRA2”                      | MERRA2                               |
+-------------------------------+--------------------------------------+
| “CMAP”                        | CMAP                                 |
+-------------------------------+--------------------------------------+
| “TRMM 3B42RT”                 | TRMM 3B42RT                          |
+-------------------------------+--------------------------------------+
| “TRMM 3B42RTV7”               | TRMM 3B42RTV7                        |
+-------------------------------+--------------------------------------+
| “TRMM 3B42V6”                 | TRMM 3B42V6                          |
+-------------------------------+--------------------------------------+
| “TRMM 3B42V7”                 | TRMM 3B42V7                          |
+-------------------------------+--------------------------------------+
| “CPC CMORPH”                  | CMORPH from CPC                      |
+-------------------------------+--------------------------------------+
| “GPM IMERG”                   | GPM IMERG data from NASA             |
+-------------------------------+--------------------------------------+
| “CPC STAGEII”                 | STAGEII from CPC                     |
+-------------------------------+--------------------------------------+
| “CPC STAGEIV”                 | STAGEIV from CPC                     |
+-------------------------------+--------------------------------------+
| “NARR”                        | North American Regional Reanalysis   |
+-------------------------------+--------------------------------------+
| “RFE2(daily)”                 | Daily rainfall estimator             |
+-------------------------------+--------------------------------------+
| “RFE2(GDAS bias-corrected)”   | RFE2 data bias corrected to GDAS     |
+-------------------------------+--------------------------------------+

+---------------------------+---------------------------------------------------------+
| 值                        | 描述                                                    |
+===========================+=========================================================+
| “CHIRPS2”                 | UCSB CHIRPS v2.0 precipitation dataset                  |
+---------------------------+---------------------------------------------------------+
| “CEOP”                    | CEOP                                                    |
+---------------------------+---------------------------------------------------------+
| “SCAN”                    | SCAN                                                    |
+---------------------------+---------------------------------------------------------+
| “GDAS(LSWG)”              | GDAS data for LSWG project                              |
+---------------------------+---------------------------------------------------------+
| “AGRMET radiation”        | AGRMET radiation                                        |
+---------------------------+---------------------------------------------------------+
| “Bondville”               | Bondville site data                                     |
+---------------------------+---------------------------------------------------------+
| “SNOTEL”                  | SNOTEL data                                             |
+---------------------------+---------------------------------------------------------+
| “COOP”                    | COOP data                                               |
+---------------------------+---------------------------------------------------------+
| “Rhone AGG”               | Rhone AGG forcing data                                  |
+---------------------------+---------------------------------------------------------+
| “VIC processed forcing”   | VIC processed forcing                                   |
+---------------------------+---------------------------------------------------------+
| “PALS station forcing”    | PALS station forcing                                    |
+---------------------------+---------------------------------------------------------+
| “PILDAS”                  | PILDAS                                                  |
+---------------------------+---------------------------------------------------------+
| “PET USGS”                | USGS PET 1.0 deg                                        |
+---------------------------+---------------------------------------------------------+
| “NAM242”                  | NAM 242 AWIPS Grid -- Over Alaska “WRFout” WRF output   |
+---------------------------+---------------------------------------------------------+
| “RDHM.3.5.6”              | DHM 3.5.6 (SAC-HTET and SNOW-17)R                       |
+---------------------------+---------------------------------------------------------+
| “LDT-generated”           | LDT-generated forcing files                             |
+---------------------------+---------------------------------------------------------+
| “CLIM-Standard”           | Forcing climatologies (LDT-generated)                   |
+---------------------------+---------------------------------------------------------+
| “GenEnsFcst”              | Generic ensemble forecast                               |
+---------------------------+---------------------------------------------------------+
| “AWAP”                    | AWAP precipitation data                                 |
+---------------------------+---------------------------------------------------------+
| “GDAS T1534”              | NCEP-specific GDAS T1534 forcing data                   |
+---------------------------+---------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Met forcing sources: GDAS

``Topographic correction method (met forcing)``:
指定是否使用高程修正进行基本驱动。可选值有：

+------------------+--------------------------------+
| 值               | 描述                           |
+==================+================================+
| “none”           | 不对驱动数据进行地形校正       |
+------------------+--------------------------------+
| “lapse-rate”     | 使用失效率对驱动数据进行校正   |
+------------------+--------------------------------+
| “slope-aspect”   | 使用坡度对驱动数据进行校正     |
+------------------+--------------------------------+

*lis.config* 输入示例

.. code:: shell

    Topographic correction method (met forcing):  "lapse-rate"

``Enable spatial downscaling of precipitation``:指定是否对降水进行空间降采样。可选值有：

+------+--------------------+
| 值   | 描述               |
+======+====================+
| 0    | 不启用空间降采样   |
+------+--------------------+
| 1    | 启用空间将采样     |
+------+--------------------+

*lis.config* 输入示例

.. code:: shell

    Enable spatial downscaling of precipitation: 0

``Spatial interpolation method (met forcing)``:
指定要应用于驱动数据的插值方案的类型。可接受的值是：

+---------------------+----------------+
| 值                  | 描述           |
+=====================+================+
| “bilinear”          | 双线性插值法   |
+---------------------+----------------+
| “budget-bilinear”   | 保守方法       |
+---------------------+----------------+
| “neighbor”          | 最近邻法       |
+---------------------+----------------+

双线性插值使用4个相邻点来计算插值权重。保守的方法使用25个相邻点。如果打开保守选项，则仅用于插入降水场（用于存储水）。其他字段仍将使用双线性选项进行插值。

*lis.config* 输入示例

.. code:: shell

    Spatial interpolation method (met forcing): bilinear

``Spatial upscaling method (met forcing)``:
用于气象驱动数据的上采样方法。可选值有：

+-------------+----------+
| 值          | 描述     |
+=============+==========+
| “average”   | 平均法   |
+-------------+----------+

请注意并不是所有的气象驱动读取器都支持气象数据的上采样。

*lis.config* 输入示例

.. code:: shell

    Spatial upscaling method (met forcing): average

``Temporal interpolation method (met forcing)``:指定要应用于强制数据的时间插值方案的类型。可选值有：

+-------------+-----------------+
| 值          | 描述            |
+=============+=================+
| linear      | 线性方法        |
+-------------+-----------------+
| trilinear   | uber next方法   |
+-------------+-----------------+

线性时间插值方法基于两点计算时间权重。
Ubernext根据三个点计算权重。目前，ubernext 选项仅针对 GSWP 驱动有效。

*lisonfig* 输入示例

.. code:: shell

    Temporal interpolation method (met forcing): linear

``Enable new zterp correction (met forcing)``: 指定是否启用新的
zterp校正。可选值有：

+-----------+----------+
| 值        | 描述     |
+===========+==========+
| .false.   | 不启用   |
+-----------+----------+
| .true.    | 启用     |
+-----------+----------+

默认不启用

这是一个标量选项，而不是每个嵌套。

这个新的 zterp
校正解决了在日出/日落时可能在一些驱动数据集中以小时间步长（例如15mn）运行时可能发生的问题。在一些孤立的案例中，SWdown
可能会出现大量不切实际的尖峰。此校正消除了尖峰。它还可以影响日出/日落前后的
SWdown，最高可达200 W/
m2。建议用户运行自己的测试并查看SWdown以确定哪种设置最适合他们。

要与较旧的LIS运行进行比较，请将此选项设置为.false ..

*lis.config* 输入示例

.. code:: shell

    Enable new zterp correction (met forcing): .false.

9.2. 运行时配置
---------------

``Forcing variables list file``:
指定包含要使用的强制变量列表的文件。请参阅示例 forcing\_variables.txt
(输入驱动变量的规范) 文件来查看规范描述。

*lis.config* 输入示例

.. code:: shell

    Forcing variables list file:     ./input/forcing_variables.txt

``Output methodology``\ ：指定是将输出写为仅包含地面点的一维数组还是包含陆点和水点的二维数组。
一维 tile 空间包括子网格图块和合奏。 一维 tile
空间包括矢量化的仅陆地网格平均值集。可选值有：

+------------------+--------------------------+
| 值               | 描述                     |
+==================+==========================+
| “none”           | 不写入输出               |
+------------------+--------------------------+
| “1d tilespace”   | 用一维 tile 域输出数据   |
+------------------+--------------------------+
| “2d gridspace”   | 用二维网格域输出数据     |
+------------------+--------------------------+
| “1d gridspace”   | 用一维网格域输出数据     |
+------------------+--------------------------+

*lis.config* 输入示例

.. code:: shell

    Output methodology: "2d gridspace"

``Output model restart files``\ ：指定是否输出重启模型文件。可选值有：

+------+------------------+
| 值   | 描述             |
+======+==================+
| 0    | 不写入重启文件   |
+------+------------------+
| 1    | 写入重启文件     |
+------+------------------+

*lis.config* 输入示例

.. code:: shell

    Output model restart files:   1

``Output data format``:指定模型输出数据的格式。可选值有：

+------------+------------------------+
| 值         | 描述                   |
+============+========================+
| “binary”   | 以二进制格式写输出     |
+------------+------------------------+
| “grib1”    | 以 grib1 格式写输出    |
+------------+------------------------+
| “grib2”    | 以 grib2 格式写输出    |
+------------+------------------------+
| “netcdf”   | 以 netcdf 格式写输出   |
+------------+------------------------+

*lis.config* 输入示例

.. code:: shell

    Output data format: netcdf

``Output naming style``\ ：指定模型输出命名和它们的组织形式。

可选值有：

+------------------------------------+-------------------+
| 值                                 | 描述              |
+====================================+===================+
| “2 level hierarchy”                | “2级层次结构”     |
+------------------------------------+-------------------+
| “3 level hierarchy”                | “3级层次结构”     |
+------------------------------------+-------------------+
| “4 level hierarchy”                | “4级层次结构”     |
+------------------------------------+-------------------+
| WMO convention for weather codes   | WMO气象代码公约   |
+------------------------------------+-------------------+

*lis.config* 输入示例

.. code:: shell

    Output naming style: "3 level hierarchy"

``Output GRIB Table Version``: specifies GRIB table version.
``Output GRIB Center Id``: specifies GRIB center id.
``Output GRIB Subcenter Id``: specifies GRIB sub-center id.
``Output GRIB Grid Id``: specifies GRIB grid id.
``Output GRIB Process Id``: specifies GRIB process id.
``Output GRIB Packing Type`` : 指定用于将数据打包到 GRIB 消息中的算法。

可选值有：

+----------------+----------------------------------------------------------------------------------------------------------------+
| 值             | 描述                                                                                                           |
+================+================================================================================================================+
| grid\_simple   | grid\_simple                                                                                                   |
+----------------+----------------------------------------------------------------------------------------------------------------+
| grid\_jpeg     | grid\_jpeg (GRIB-2 only) Do not use. There is an open issue regarding packing constant data with grid\_jpeg.   |
+----------------+----------------------------------------------------------------------------------------------------------------+

虽然未进行尝试，但是在
https://software.ecmwf.int/wiki/display/GRIB/Grib+API+keys
列出了更多的可用的打包方法。

*lis.config* 输入示例

.. code:: shell

    Output GRIB Table Version: 130
    Output GRIB Center Id: 173
    Output GRIB Subcenter Id: 4
    Output GRIB Grid Id: 11
    Output GRIB Process Id: 1
    Output GRIB Packing Type: grid_simple

``start mode``: 指定使用的重启模型。可选值有：

+-------------+----------------------------------+
| 值          | 描述                             |
+=============+==================================+
| restart     | 使用重启模型                     |
+-------------+----------------------------------+
| coldstart   | 使用冷启动模式，不读取重启文件   |
+-------------+----------------------------------+

当指定冷启动选项时，使用 LSM 特定的初始条件初始化程序（通常假定所有
tiles 均匀）。当使用重启模式时，假设根据使用的LSM
提供相应的重启文件。用户还需要确保模拟的结束时间大于写入重新启动文件时的模型时间。

*lis.config* 输入示例

.. code:: shell

    Start mode: coldstart

开始时间以以下格式指定：

+--------------------+-----------------+----------------+
| 变量               | 值              | 描述           |
+====================+=================+================+
| Starting year:     | 整型2001-现在   | 指定起始年     |
+--------------------+-----------------+----------------+
| Starting month:    | 整型1-12        | 指定起始月     |
+--------------------+-----------------+----------------+
| Starting day:      | 整型1-31        | 指定起始日     |
+--------------------+-----------------+----------------+
| Starting hour:     | 整型0-23        | 指定起始小时   |
+--------------------+-----------------+----------------+
| Starting minute:   | 整型0-59        | 指定起始分钟   |
+--------------------+-----------------+----------------+
| Starting second:   | 整型0-59        | 指定起始秒     |
+--------------------+-----------------+----------------+

*lis.config* 输入示例

.. code:: shell

    Starting year: 2002
    Starting month: 10
    Starting day: 29
    Starting hour: 1
    Starting minute: 0
    Starting second: 0

结束时间以下列格式指定： \| 变量 \| 值 \| 描述 \| \| ---------------- \|
------------- \| ------------ \| \| Starting year: \| 整型2001-现在 \|
指定结束年 \| \| Starting month: \| 整型1-12 \| 指定结束月 \| \|
Starting day: \| 整型1-31 \| 指定结束日 \| \| Starting hour: \| 整型0-23
\| 指定结束小时 \| \| Starting minute: \| 整型0-59 \| 指定结束分钟 \| \|
Starting second: \| 整型0-59 \| 指定结束秒 \|

*lis.config* 输入示例

.. code:: shell

    Ending year: 2002
    Ending month: 10
    Ending day: 31
    Ending hour: 1
    Ending minute: 0
    Ending second: 0

``LIS time window interval``:
指定LIS运行循环周期的间隔，用于“整体平滑”运行模式。

*lis.config* 输入示例

.. code:: shell

    LIS time window interval:

``Undefined value``:指定未定义值。默认设置为 -9999。

*lis.config* 输入示例

.. code:: shell

    Undefined value: -9999

``Output directory``:指定顶级输出目录的名称。可接受的值是任何40个字符的字符串。默认值设置为OUTPUT。为简单起见，在本文档的其余部分中提到此顶级输出目录，使用其默认名称
*$ WORKING/LIS/OUTPUT* 。

*lis.config* 输入示例

.. code:: shell

    Output directory: OUTPUT

``Diagnostic output file``:
指定运行时诊断文件的名称。可接受的值是任何40个字符的字符串。

*lis.config* 输入示例

.. code:: shell

    Diagnostic output file: lislog

``Number of ensembles per tile``:
指定要使用的tile的集合数。该值应大于或等于1。

*lis.config* 输入示例

.. code:: shell

    Number of ensembles per tile: 1

以下选项用于基于植被的子网格平铺

``Maximum number of surface type tiles per grid``:定义每个网格的最大表面类型
tiles（这可以与植被类型的总数一样多）。

*lis.config* 输入示例

.. code:: shell

    Maximum number of surface type tiles per grid: 1

``Minimum cutoff percentage (surface type tiles)``:
定义要为其创建切片的单元格的最小百分比。百分比值表示为分数。

*lis.config* 输入示例

.. code:: shell

    Minimum cutoff percentage (surface type tiles): 0.05

``Maximum number of soil texture tiles per grid``:
定义每个网格的最大土壤纹理 tile（这可以与土壤纹理类型的总数一样多）。

*lis.config* 输入示例

.. code:: shell

    Maximum number of soil texture tiles per grid: 1

``Minimum cutoff percentage (soil texture tiles)``:定义要为其创建 tile
的单元格的最小百分比。百分比值表示为分数。

*lis.config* 输入示例

.. code:: shell

    Minimum cutoff percentage (soil texture tiles): 0.05

``Maximum number of soil fraction tiles per grid``:
定义每个网格的最大土壤 tiles 分类数
（这可以与土壤分数类型的总数一样多）。

*lis.config* 输入示例

.. code:: shell

    Maximum number of soil fraction tiles per grid: 1

``Minimum cutoff percentage (soil fraction tiles)``:定义要为其创建 tile
所占单元格的最小百分比。百分比值表示为分数。

*lis.config* 输入示例

.. code:: shell

    Minimum cutoff percentage (soil fraction tiles): 0.05

``Maximum number of elevation bands per grid``:定义每个网格的最大高程波段（这可以是高程波段类型的总数）

*lis.config* 输入示例

.. code:: shell

    Maximum number of elevation bands per grid: 1

``Minimum cutoff percentage (elevation bands):``\ 定义要为其创建 tile
所占单元格的最小百分比。百分比值表示为分数。

*lis.config* 输入示例

.. code:: shell

    Minimum cutoff percentage (elevation bands): 0.05

``Maximum number of slope bands per grid``:定义每个网格的最大坡度波段输（这可以是坡度波段类型的总数）。

*lis.config* 输入示例

.. code:: shell

    Maximum number of slope bands per grid: 1

``Minimum cutoff percentage (slope bands)``:定义要为其创建 tile
所占单元格的最小百分比。百分比值表示为分数。

*lis.config* 输入示例

.. code:: shell

    Minimum cutoff percentage (slope bands): 0.05

``Maximum number of aspect bands per grid:``\ 定义每个网格的最大坡向波段输（这可以是坡向波段类型的总数）。

*lis.config* 输入示例

.. code:: shell

    Maximum number of aspect bands per grid: 1

``Minimum cutoff percentage (aspect bands)``:定义要为其创建 tile
所占单元格的最小百分比。百分比值表示为分数。

*lis.config* 输入示例

.. code:: shell

    Minimum cutoff percentage (aspect bands): 0.05

本节指定并行处理环境中处理器的二维布局。用户可以通过两种方式指定二维布局。

一种方法是用户可以分别使用沿\ ``Number of processors along x``\ ：和\ ``Number of processors along y:``\ 来分别指定沿东西方向和南北方向的处理器数量。请注意，处理器的布局应与使用的处理器总数相匹配。例如，如果使用8个处理器，则布局可以指定为1x8,2x4,4x2或8x1。选择这种方式时，选项
``Decompose by processes``\ ：必须设置为
``.false.``.当必须匹配特定布局时，这种方式很有用。

另一种方式是用户可以允许 LIS
根据进程数创建负载平衡布局。例如，如果使用8个处理器，LIS
将创建一个4x2布局，其中每个进程包含大致相同数量的基于陆地的网格单元。选择这种方式时，LIS
会忽略\ ``Number of processors along x``\ ：和\ ``Number of processors along y:``\ 选项。当您的运行域包含大量基于海洋的网格单元时,使用指定布局时导致许多未充分利用的进程
，这种方式很有用。

``Decompose by processes``\ 的可选值有：

+-----------+---------------------------------------------------------------------------------------------------------------------+
| 值        | 描述                                                                                                                |
+===========+=====================================================================================================================+
| .false.   | 不要按进程分解。使用\ ``Number of processors along x``\ ：和\ ``Number of processors along y:``\ 创建指定的布局。   |
+-----------+---------------------------------------------------------------------------------------------------------------------+
| .true.    | 按进程分解                                                                                                          |
+-----------+---------------------------------------------------------------------------------------------------------------------+

默认设置为 .false.

此外，本节还允许在每个处理器上使用 ``Halo size along x:``\ 和\` Halo
size along y: 来规定晕域周围的晕圈。

*lis.config* 输入示例

.. code:: shell

    Decompose by processes: .false.
    Number of processors along x: 2
    Number of processors along y: 2
    Halo size along x: 0
    Halo size along y: 0

``Routing model``:选定运行使用的路由模型。可选值有：

+------------------+-------------------+
| 值               | 描述              |
+==================+===================+
| none             | 不适用路由模型    |
+------------------+-------------------+
| NLDAS router”    | 使用 NLDAS 路由   |
+------------------+-------------------+
| “HYMAP router”   | 使用 HyMAP 路由   |
+------------------+-------------------+

``External runoff data source:``
指定用于读取离线路由的曲面径流和基流字段的数据源。

可选值有：

+----------------------------------+-----------------------------------------------------+
| 值                               | 描述                                                |
+==================================+=====================================================+
| "LIS runoff output"              | 使用 LIS 输出                                       |
+----------------------------------+-----------------------------------------------------+
| "GLDAS1 runoff data"             | 使用 GLDAS1 输出                                    |
+----------------------------------+-----------------------------------------------------+
| "GLDAS2 runoff data"             | 使用 GLDAS2 输出                                    |
+----------------------------------+-----------------------------------------------------+
| "NLDAS2 runoff data"             | 使用 NLDAS2 输出                                    |
+----------------------------------+-----------------------------------------------------+
| "MERRA2 runoff data"             | 使用 MERR2 输出                                     |
+----------------------------------+-----------------------------------------------------+
| "ERA interim land runoff data"   | 使用 ERA-Interim-Land 输出                          |
+----------------------------------+-----------------------------------------------------+
| "GWB MIP runoff data"            | 使用 Global Water Budget (GWB) model 比对项目产出   |
+----------------------------------+-----------------------------------------------------+

``Number of application models:``\ 指定运行所用应用模型数

*lis.config* 输入示例

.. code:: shell

    Routing model: none
    External runoff data source: "LIS runoff output"
    Radiative transfer model: none
    Number of application models: 0

9.3. 预测运行模式
-----------------

本节指定预测选项的选择 。

``Forecast forcing source mode:``\ 指定预测运行模式和源选项（例如，集合流量预测或ESP），并取决于所选的驱动数据集的数量。可选值有：

+----------------------+----------------+
| 值                   | 描述           |
+======================+================+
| “ESP conventional”   | ESP 传统模式   |
+----------------------+----------------+
| “ESP boot”           | ESP 引导模式   |
+----------------------+----------------+

*lis.config* 输入示例

.. code:: shell

    Forecast forcing source mode: "ESP conventional"

``ESP conventional start time of the forcing archive:``\ 指定 ESP
传统模式驱动开始日期（YYYY MM DD）。

*lis.config* 输入示例

.. code:: shell

    ESP conventional start time of the forcing archive: 1982 1 1

``ESP conventional end time of the forcing archive:``\ 指定 ESP
传统模式驱动结束日期（YYYY MM DD）。

*lis.config* 输入示例

.. code:: shell

    ESP conventional end time of the forcing archive: 1982 1 1

``ESP boot sampling time window interval:``\ 定ESP引导（“boot”）时间采样窗口。

``ESP boot start time of the forcing archive:``
指定ESP引导（“boot”）模式开始日期（YYYY MM DD）。

``ESP boot end time of the forcing archive:``
指定ESP引导（“boot”）模式结束日期（YYYY MM DD）。

*lis.config* 输入示例

``shell ESP boot sampling time window interval: "10da" ESP boot start time of the forcing archive: 1982 1 1 ESP boot end time of the forcing archive: 2010 1 1``

``Forecast forcing start mode:``\ 指定预测启动模式的类型，冷启动或重启。如果指定了
restart，则需要提供重新启动文件名。

*lis.config* 输入示例

.. code:: shell

    Forecast forcing start mode: "coldstart"

``Forecast forcing restart filename:`` 指定重启文件名

*lis.config* 输入示例

.. code:: shell

    Forecast forcing restart filename: "LIS_RST_CLSMF25_201005050000.d01.nc"

9.4. 数据同化
-------------

本节指定数据同化选项的选择。

``Number of data assimilation instances:``
指定数据同化实例的数量。有效值为0（无同化）或更高。

*lis.config* 输入示例

.. code:: shell

    Number of data assimilation instances: 0

``Data assimilation algorithm:`` 指定数据同化算法的选择。可选值有：

+----------------------+--------------------------------+
| 值                   | 描述                           |
+======================+================================+
| “none”               | 无                             |
+----------------------+--------------------------------+
| “Direct insertion”   | 直接插入                       |
+----------------------+--------------------------------+
| “EnKF”               | GMAO EnKF 数据同化             |
+----------------------+--------------------------------+
| “EnKS”               | GRACE 集合卡尔曼滤波数据同化   |
+----------------------+--------------------------------+

*lis.config* 输入示例

.. code:: shell

    Data assimilation algorithm: none

``Data assimilation set:``\ 指定“同化集”，它是与特定观察的同化相关的实例。可选值有：

+--------------------------------+-----------------------------------------------+
| 值                             | 描述                                          |
+================================+===============================================+
| “none”                         | none                                          |
+--------------------------------+-----------------------------------------------+
| “AMSR-E(NASA) soil moisture”   | AMSRE L3 土壤湿度每日网格化数据（HDF 格式）   |
+--------------------------------+-----------------------------------------------+
| “AMSR-E(LPRM) soil moisture”   | AMSRE L3 土壤湿度每日网格化数据（HDF 格式）   |
+--------------------------------+-----------------------------------------------+
| “ESA CCI soil moisture”        | ESA CCI 土壤湿度                              |
+--------------------------------+-----------------------------------------------+
| “Windsat”                      | Windsat                                       |
+--------------------------------+-----------------------------------------------+
| “ANSA SCF”                     | ANSA SCF                                      |
+--------------------------------+-----------------------------------------------+
| “ANSA snow depth”              | ANSA 积雪厚度                                 |
+--------------------------------+-----------------------------------------------+
| “SMMR snow depth”              | SMMR 积雪厚度                                 |
+--------------------------------+-----------------------------------------------+
| “SMMI snow depth”              | SMMI 积雪厚度                                 |
+--------------------------------+-----------------------------------------------+
| “PMW snow”                     | PMW-based SWE or snow depth                   |
+--------------------------------+-----------------------------------------------+
| “MODIS SCF”                    | MODIS SCF                                     |
+--------------------------------+-----------------------------------------------+

+----------------------------------+-----------------------------+
| 值                               | 描述                        |
+==================================+=============================+
| “GRACE TWS”                      | GRACE TWS                   |
+----------------------------------+-----------------------------+
| “SMOPS soil moisture”            | SMOPS 土壤湿度              |
+----------------------------------+-----------------------------+
| “GCOMW AMSR2 L3 snow depth”      | AMSR2 (GCOMW) L3 积雪厚度   |
+----------------------------------+-----------------------------+
| “GCOMW AMSR2 L3 soil moisture”   | AMSR2 (GCOMW) 土壤湿度      |
+----------------------------------+-----------------------------+
| “SMAP(NASA) soil moisture”       | NASA SMAP 土壤湿度          |
+----------------------------------+-----------------------------+
| “PILDAS SM”                      | PILDAS 土壤湿度             |
+----------------------------------+-----------------------------+
| “SMOS L2 soil moisture”          | SMOS L2 土壤湿度            |
+----------------------------------+-----------------------------+
| “SMOS(NESDIS) soil moisture”     | NESDIS SMOS 土壤湿度        |
+----------------------------------+-----------------------------+

*lis.config* 输入示例

.. code:: shell

    Data assimilation set: none

``Data assimilation exclude analysis increments:``\ 指定是否跳过分析增量。该选项通常与动态偏差估计算法一起使用。用户可以选择仅应用偏置增量或偏置增量和分析增量。可选值有：

+------+--------------------+
| 值   | 描述               |
+======+====================+
| 0    | 应用分析增量       |
+------+--------------------+
| 1    | 不要应用分析增量   |
+------+--------------------+

*lis.config* 输入示例

.. code:: shell

    Data assimilation exclude analysis increments: 0

``Data assimilation output interval for diagnostics:``\ 指定输出诊断间隔。有关如何指定时间间隔的说明，请参见定义时间间隔一节。

*lis.config* 输入示例

.. code:: shell

    Data assimilation output interval for diagnostics: 1da

``Data assimilation number of observation types:``\ 指定同化中使用的观察物种/类型的数量。

*lis.config* 输入示例

.. code:: shell

    Data assimilation number of observation types: 0

``Data assimilation output ensemble spread:``\ 指定是否输出整体传播。可选值有：

+------+--------------------+
| 值   | 描述               |
+======+====================+
| 0    | 不要输出整体成员   |
+------+--------------------+
| 1    | 输出整体成员       |
+------+--------------------+

*lis.config* 输入示例

.. code:: shell

    Data assimilation output processed observations: 0

``Data assimilation output innovations:``\ 指定是否要写入规范化更新的二进制输出。
可选值有：

+------+--------------+
| 值   | 描述         |
+======+==============+
| 0    | 不输出更新   |
+------+--------------+
| 1    | 输出更新     |
+------+--------------+

*lis.config* 输入示例

.. code:: shell

    Data assimilation output innovations: 0

``Data assimilation use a trained forward model:``\ 指定是否使用经过训练的正向模型。

+------+----------------------------+
| 值   | 描述                       |
+======+============================+
| 0    | 不使用经过训练的正向模型   |
+------+----------------------------+
| 1    | 使用经过训练的正向模型     |
+------+----------------------------+

``Data assimilation trained forward model output file:``\ 指定训练的正向模型的输出文件的名称。训练由
LDT 完成，因此，该文件由 LDT 生成。

*lis.config* 输入示例

.. code:: shell

    Data assimilation use a trained forward model: 0
    Data assimilation trained forward model output file: none

``Data assimilation scaling strategy:``\ 指定缩放策略 。可选值有：

+--------------------+-----------------------+
| 值                 | 描述                  |
+====================+=======================+
| none               | 不使用任何缩放        |
+--------------------+-----------------------+
| "Linear scaling"   | 使用线性缩放方法      |
+--------------------+-----------------------+
| "CDF matching"     | 用CDF匹配来扩展观察   |
+--------------------+-----------------------+

*lis.config* 输入示例

.. code:: shell

    Data assimilation scaling strategy: none

``Data assimilation observation domain file:``\ 指定观察域文件，该文件将用作计算创新的域。

*lis.config* 输入示例

.. code:: shell

    Data assimilation observation domain file: ascat_cdf_domain.nc

9.5. 偏差估计
-------------

``Bias estimation algorithm:``\ 指定要使用的动态偏差估计算法。
可选值有：

+------------------------------+--------------------------+
| 值                           | 描述                     |
+==============================+==========================+
| “none”                       | 没有动态偏差估计         |
+------------------------------+--------------------------+
| “Adaptive bias correction”   | NASA GMAO 动态偏差估计   |
+------------------------------+--------------------------+

*lis.config* 输入示例

.. code:: shell

    Bias estimation algorithm: none

``Bias estimation attributes file:``
ASCII文件，指定偏差估计的属性。下面显示了一个示例文件，该文件首先列出了变量名称。接下来是
nparam
值（0-无偏差校正，1-恒定偏差校正，无昼夜循环，3-昼夜正弦/余弦偏差校正，5-半昼夜正弦/余弦偏差校正，2-“时间”）偏差校正每天2个单独的偏差估计，4个“一天中的时间”偏差校正，每天4个单独的估计，8个
-
“一天中的时间”偏差校正，每天8个独立的偏差估计），tconst（描述时间）相对于观察的时间间隔的比例）和
trelax（指定时间弛豫参数，以秒为单位）

*Example bias estimation attributes file*

nparam tconst trelax

Soil Temperature

1.0 0.05 86400.0

*lis.config* 输入示例

.. code:: shell

    Bias estimation attributes file:

``Bias estimation restart output frequency:``\ 指定偏置重启文件的频率。

有关如何指定时间间隔的说明，请参见定义时间间隔一节。

*lis.config* 输入示例

.. code:: shell

    Bias estimation restart output frequency: 1da

``Bias estimation start mode:``\ 此选项指定是否从检查点文件中读取偏差参数。
可选值有：

+--------+------------------------+
| 值     | 描述                   |
+========+========================+
| none   | 不要使用偏差重启文件   |
+--------+------------------------+
| read   | 使用偏差重启文件       |
+--------+------------------------+

*lis.config* 输入示例

.. code:: shell

    Bias estimation start mode: none

``Bias estimation restart file:``\ 指定用于初始化偏差参数的重新启动文件

*lis.config* 输入示例

.. code:: shell

    Bias estimation restart file: none

``Perturbations start mode:``\ 指定是否应从重新启动文件中读取扰动设置。
可选值有：

+-------------+----------------+
| 值          | 描述           |
+=============+================+
| coldstart   | 无（冷启动）   |
+-------------+----------------+
| read        | 使用重启文件   |
+-------------+----------------+

*lis.config* 输入示例

.. code:: shell

    Perturbations start mode: coldstart

``Apply perturbation bias correction:``\ 指定是否应用Ryu等人。算法（JHM
2009），强制和建模状态以避免由扰动引起的不希望的偏差。 可选值有：

+------+----------+
| 值   | 描述     |
+======+==========+
| 0    | 不应用   |
+------+----------+
| 1    | 应用     |
+------+----------+

*lis.config* 输入示例

.. code:: shell

    Apply perturbation bias correction:

``Perturbations restart output interval:``\ 指定扰动重启输出写入间隔。

有关如何指定时间间隔的说明，请参见定义时间间隔一节。

*lis.config* 输入示例

.. code:: shell

    Perturbations restart output interval: 1da

``Perturbations restart filename:``\ 指定重新启动文件的名称，如果未使用冷启动选项，则用于初始化扰动设置。

*lis.config* 输入示例

.. code:: shell

    Perturbations restart filename: none

``Forcing perturbation algorithm:``\ 指定扰动驱动变量的算法。可选值有：

+-----------------+-----------------+
| 值              | 描述            |
+=================+=================+
| “none”          | None            |
+-----------------+-----------------+
| “GMAO scheme”   | GMAO 扰动算法   |
+-----------------+-----------------+

*lis.config* 输入示例

.. code:: shell

    Forcing perturbation algorithm: none

``Forcing perturbation frequency:``\ 指定驱动扰动间隔

有关如何指定时间间隔的说明，请参见定义时间间隔一节。

*lis.config* 输入示例

.. code:: shell

    Forcing perturbation frequency: 1hr

``Forcing attributes file:``\ 指定强制属性的 ASCII
文件（用于扰动）下面显示了一个示例文件，其中列出了3个驱动变量。对于每个变量，首先指定变量的名称，然后指定下一行中的最小值和最大值。对每个附加变量重复此操作。

    *Example forcing attributes file* varmin varmax Incident Shortwave
    Radiation Level 001 0.0 1300.0 Incident Longwave Radiation Level 001
    -50.0 800.0 Rainfall Rate Level 001 0.0 0.001

*lis.config* 输入示例

.. code:: shell

    Forcing attributes file: none

``Forcing perturbation attributes file:``\ ASCII
文件，指定驱动扰动的属性。下面显示了一个示例文件，其中列出了3个驱动变量。每个变量有三行规范。第一行指定变量的名称。第二行指定扰动类型（0-加法，1-乘法）和标准偏差的扰动类型（0-加法，1-乘法）。第三行按顺序指定以下值：扰动的标准偏差，标准偏差系数（如果标准偏差的扰动类型为1），标准正常最大值，是否在扰动中启用零均值，时间相关比例（以秒为单位）
，x和y相关，最后是与其他变量的互相关。

    *Example forcing perturbation attributes file* ptype std std\_max
    zeromean tcorr xcorr ycorr ccorr Incident Shortwave Radiation Level
    001 1 0 0.50 2.5 1 86400 0 0 1.0 -0.5 -0.8 Incident Longwave
    Radiation Level 001 0 1 50.0 0.2 2.5 1 86400 0 0 -0.5 1.0 0.5
    Rainfall Rate Level 001 1 0 0.50 2.5 1 86400 0 0 0.8 0.5 1.0

*lis.config* 输入示例

.. code:: shell

    Forcing perturbation attributes file: none

``State perturbation algorithm:``\ 指定扰动状态预测变量的算法。
可选值有：

+-----------------+-----------------+
| 值              | 描述            |
+=================+=================+
| “none”          | None            |
+-----------------+-----------------+
| “GMAO scheme”   | GMAO 扰动算法   |
+-----------------+-----------------+

*lis.config* 输入示例

.. code:: shell

    State perturbation algorithm: none

``State perturbation frequency:`` 指定预测变量扰动间隔。

有关如何指定时间间隔的说明，请参见定义时间间隔一节。

*lis.config* 输入示例

.. code:: shell

    State perturbation frequency: 1hr

``State attributes file:``\ ASCII文件，指定预测变量的属性。下面显示了一个示例文件，其中列出了2个模型状态变量。对于每个变量，首先指定变量的名称，然后指定下一行中的最小值和最大值。对每个附加变量重复此操作。

    *Example state attributes file* name varmin varmax SWE 0.0 100.0
    Snowdepth 0.0 100.0

*lis.config* 输入示例

.. code:: shell

    State attributes file: none

``State perturbation attributes file:``\ ASCII
文件，指定预测变量扰动的属性。下面提供了一个示例文件，其格式与驱动扰动属性文件的格式相同：

    *Example state perturbation attributes file* #perttype std std\_max
    zeromean tcorr xcorr ycorr ccorr SWE 1 0 0.01 2.5 1 10800 0 0 1.0
    0.9 Snowdepth 1 0 0.02 2.5 1 10800 0 0 0.9 1.0

*lis.config* 输入示例

.. code:: shell

    State perturbation attributes file: none

``Observation perturbation algorithm:``\ 指定扰动观测的算法。 可选值有：

+-----------------+-----------------+
| 值              | 描述            |
+=================+=================+
| “none”          | None            |
+-----------------+-----------------+
| “GMAO scheme”   | GMAO 扰动算法   |
+-----------------+-----------------+

*lis.config* 输入示例

.. code:: shell

    Observation perturbation algorithm: none

``Observation perturbation frequency:``\ 指定观察扰动间隔。

有关如何指定时间间隔的说明，请参见定义时间间隔一节。

*lis.config* 输入示例

.. code:: shell

    Observation perturbation frequency: 1hr

``Observation attributes file:``\ ASCII文件，指定观察变量的属性。下面提供了一个示例文件，其格式与驱动属性文件和状态属性文件的格式相同
。

    *Example observation attributes file* #error rate varmin varmax ANSA
    SWE 10.0 0.01 500

*lis.config* 输入示例

.. code:: shell

    Observation attributes file: none

``Observation perturbation attributes file:``\ ASCII文件，指定观察变量扰动的属性。下面提供了一个示例文件，其格式与驱动扰动属性文件的格式相同：

    *Example observation perturbation attributes file* #perttype std
    std\_max zeromean tcorr xcorr ycorr ccorr ANSA SWE 0 10 2.5 1 10800
    0 0 1

*lis.config* 输入示例

.. code:: shell

    Observation perturbation attributes file: none

``IMS data directory：``\ 指定IMS数据的位置。

*lis.config* 输入示例

.. code:: shell

    IMS data directory:

9.5.1. PILDAS 土壤湿度同化
~~~~~~~~~~~~~~~~~~~~~~~~~~

``PILDAS soil moisture data directory:`` 指定 PILDAS
土壤湿度数据的目录。 ``PILDAS use scaled standard deviation model:``
指定是否使用模型和观察标准偏差来缩放观察误差标准偏差。
``PILDAS soil moisture model CDF file:`` 指定模型 CDF
文件的名称（观测将扩展到此气候学）
``PILDAS soil moisture observation CDF file:`` 指定观察 CDF 文件的名称。
``PILDAS soil moisture number of bins in the CDF:`` 指定 CDF 中的 bin
数。

*lis.config* 输入示例

.. code:: shell

    PILDAS soil moisture data directory: ./input/...
    PILDAS use scaled standard deviation model: 1
    PILDAS soil moisture model CDF file: lsm.cdf.nc
    PILDAS soil moisture observation CDF file: obs_cdf.nc
    PILDAS soil moisture number of bins in the CDF:

9.5.2. AMSR-E（NASA）土壤水分同化
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``NASA AMSR-E soil moisture data directory:`` 指定AMSR-E（NASA /
NSIDC）土壤湿度数据的目录。
``NASA AMSR-E soil moisture scale observations:``
指定是否要重新调整观察结果（使用 CDF 匹配）。
``NASA AMSR-E soil moisture model CDF file:`` 指定模型 CDF
文件的名称（观测值将缩放到此气候学中）。
``NASA AMSR-E soil moisture observation CDF file:`` 指定观察 CDF
文件的名称。 ``NASA AMSR-E soil moisture number of bins in the CDF:``
指定 CDF 中的 bin 数。 *lis.config* 输入示例

.. code:: shell

    NASA AMSR-E soil moisture data directory: 'input'
    NASA AMSR-E soil moisture scale observations: 1
    NASA AMSR-E soil moisture model CDF file: lsm_cdf.nc
    NASA AMSR-E soil moisture observation CDF file: obs_cdf.nc
    NASA AMSR-E soil moisture number of bins in the CDF: 100

9.5.3. AMSR-E（LPRM）土壤水分同化
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``AMSR-E(LPRM) soil moisture data directory:``\ 指定
AMSR-E（LPRM）土壤湿度数据的目录。
``AMSR-E(LPRM) soil moisture use raw data:``
指定是否使用原始字段（以湿度单位表示）或缩放字段（以体积单位表示）。
``AMSR-E(LPRM) use scaled standard deviation model:``\ 指定是否使用模型和观察标准偏差来缩放观察误差标准偏差。
``AMSR-E(LPRM) model CDF file:`` 指定模型 CDF
文件的名称（观测值将缩放到此气候学中）。
``AMSR-E(LPRM) observation CDF file:``\ 指定观测 CDF 文件的名称。
``AMSR-E(LPRM) soil moisture number of bins in the CDF:``\ 指定 CDF 中的
bin 数。

*lis.config* 输入示例

.. code:: shell

    AMSR-E(LPRM) soil moisture data directory:       'input' 
    AMSR-E(LPRM) soil moisture use raw data:          0
    AMSR-E(LPRM) use scaled standard deviation model: 1 
    AMSR-E(LPRM) model CDF file:                      lsm_cdf.nc 
    AMSR-E(LPRM) observation CDF file:                obs_cdf.nc 
    AMSR-E(LPRM) soil moisture number of bins in the CDF: 100

9.5.4. WindSat 土壤水分同化
~~~~~~~~~~~~~~~~~~~~~~~~~~~

``WindSat soil moisture data directory:`` 指定 WindSat
土壤湿度数据的目录。 ``WindSat scale observations:``
指定是否要重新调整观察结果（使用 CDF 匹配）。
``WindSat model CDF file:`` 指定模型 CDF
文件的名称（观测值将缩放到此气候学中）。
``WindSat observation CDF file:`` 指定观测 CDF 文件的名称。
``WindSat number of bins in the CDF:`` 指定 CDF 中的 bin 数。

*lis.config* 输入示例

.. code:: shell

    WindSat soil moisture data directory:       'input'
    WindSat scale observations:                  1 
    WindSat model CDF file:                      lsm_cdf.nc 
    WindSat observation CDF file:                obs_cdf.nc 
    WindSat number of bins in the CDF:           100

9.5.5. ANSA 积雪分数（SCF）同化
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ANSA SCF data directory: specifies the directory for the ANSA SCA data.
ANSA SCF lower left lat: specifies the lower left latitude of the ANSA
domain. (cylindrical latitude/longitude projection) ANSA SCF lower left
lon: specifies the lower left longitude of the ANSA domain. (cylindrical
latitude/longitude projection) ANSA SCF upper right lat: specifies the
upper right latitude of the ANSA domain. (cylindrical latitude/longitude
projection) ANSA SCF upper right lon: specifies the upper right
longitude of the ANSA domain. (cylindrical latitude/longitude
projection) ANSA SCF resolution (dx): specifies the resolution of the of
the ANSA domain along the east-west direction. ANSA SCF resolution (dy):
specifies the resolution of the of the ANSA domain along the north-south
direction. ANSA SCF local time for assimilation: specifies the local
time for performing the ANSA SCF assimilation; LIS will find the closest
time depending on model timestep. ANSA SCF field name: specifies the
name of the SCF field to be assimilated in the ANSA SCF data file. ANSA
SCF file name convention: specifies the name convention of the ANSA SCF
file; currently supported: *YYYYMMDD*, *YYYYDOY*. ANSA SCF use
triangular-shaped observation error: specifies whether to use a
triangular-shaped observation error as follows (De Lannoy et al., 2012):
if ; if ; otherwise, remains to be the same as read in from the
observation perturbation attributes file. ANSA SCF using EnKF with DI:
specifies whether to used rule-based direct insertion approach to
supplement EnKF when model predicts zero or full snow cover for all
ensemble members. The entries after this are needed only if 1 is
specified here.

9.8. 参数
---------

``LIS domain and parameter data file:``\ 指定包含 LIS
参数数据的主输入文件。

LIS 7包括一个称为 Land Data
Toolkit（LDT）的预处理系统。它读取原始参数数据并将其处理到 LIS 运行域。
``LIS domain and parameter data file:``\ 是LDT预处理的结果。有关更多信息，请阅读“Land
Data Toolkit（LDT）用户指南”。

*lis.config* 输入示例

.. code:: shell

    LIS domain and parameter data file: ./lis_input.d01.nc

9.8.1. 参数选项
~~~~~~~~~~~~~~~

``Landmask data source:``\ 指定运行中 landmask 数据的用法。可选值有：

+--------+---------------------------------------------------------------------+
| 值     | 描述                                                                |
+========+=====================================================================+
| none   | 不使用 landmask                                                     |
+--------+---------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 landmask   |
+--------+---------------------------------------------------------------------+

``Landcover data source:``\ 指定运行中的 landcover
数据的使用情况。可选值有：

+-------+----------------------------------------------------------------------+
| 值    | 描述                                                                 |
+=======+======================================================================+
| LDT   | 从 LDT 中产生的 LIS domain and parameter data file: 读取 landcover   |
+-------+----------------------------------------------------------------------+

``Soil texture data source:``\ 指定运行中土壤纹理数据的用法。 可选值有：

+--------+-------------------------------------------------------------------------+
| 值     | 描述                                                                    |
+========+=========================================================================+
| none   | 不使用 Soil texture                                                     |
+--------+-------------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 Soil texture   |
+--------+-------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Soil texture data source: LDT

``Soil fraction data source:``\ 指定运行中土壤组分数据的用法。
可选值有：

+--------+--------------------------------------------------------------------------+
| 值     | 描述                                                                     |
+========+==========================================================================+
| none   | 不使用 Soil texture                                                      |
+--------+--------------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 Soil fraction   |
+--------+--------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Soil fraction data source: LDT

``Soil color data source:``\ 指定运行中土壤颜色数据的用法。 可选值有：

+--------+-----------------------------------------------------------------------+
| 值     | 描述                                                                  |
+========+=======================================================================+
| none   | 不使用 Soil color                                                     |
+--------+-----------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 Soil color   |
+--------+-----------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Soil color data source: LDT

``Elevation data source:``\ 指定运行中高程数据的用法。 可选值有：

+--------+--------------------------------------------------------------------+
| 值     | 描述                                                               |
+========+====================================================================+
| none   | 不使用 Elevation data                                              |
+--------+--------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取高程数据   |
+--------+--------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Elevation data source: LDT

``Slope data source:``\ 指定运行中坡度数据的用法。 可选值有：

+--------+--------------------------------------------------------------------+
| 值     | 描述                                                               |
+========+====================================================================+
| none   | 不使用 Slope data                                                  |
+--------+--------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取坡度数据   |
+--------+--------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Slope data source: LDT

``Aspect data source:``\ 指定运行中坡向数据的用法。 可选值有：

+--------+--------------------------------------------------------------------+
| 值     | 描述                                                               |
+========+====================================================================+
| none   | 不使用 Aspect data                                                 |
+--------+--------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取坡向数据   |
+--------+--------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Aspect data source: LDT

``Curvature data source:``\ 指定运行中曲率 数据的用法。 可选值有：

+--------+----------------------------------------------------------------------+
| 值     | 描述                                                                 |
+========+======================================================================+
| none   | 不使用 Curvature data                                                |
+--------+----------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 曲率 数据   |
+--------+----------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Curvature data source: LDT

``LAI data source:``\ 指定运行中叶面积指数数据的用法。 可选值有：

+---------------------+--------------------------------------------------------------------------+
| 值                  | 描述                                                                     |
+=====================+==========================================================================+
| none                | 不使用 LAI data                                                          |
+---------------------+--------------------------------------------------------------------------+
| LDT                 | 从 LDT 中产生的 LIS domain and parameter data file: 读取叶面积指数数据   |
+---------------------+--------------------------------------------------------------------------+
| “MODIS real-time”   | 读取 MODIS 实时叶面积指数                                                |
+---------------------+--------------------------------------------------------------------------+
| ALMIPII             | 读取 ALMIPII 实时叶面积指数                                              |
+---------------------+--------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    LAI data source: none

``SAI data source:``\ 指定运行中 SAI 数据的用法。 可选值有：

+---------------------+---------------------------------------------------------------------+
| 值                  | 描述                                                                |
+=====================+=====================================================================+
| none                | 不读取 SAI                                                          |
+---------------------+---------------------------------------------------------------------+
| LDT                 | 从 LDT 中产生的 LIS domain and parameter data file: 读取 SAI 数据   |
+---------------------+---------------------------------------------------------------------+
| “MODIS real-time”   | 读取 MODIS 实时 SAI                                                 |
+---------------------+---------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    SAI data source: none

``Albedo data source:``\ 指定运行中反照率数据的用法。 可选值有：

+-----------+------------------------------------------------------------------------+
| 值        | 描述                                                                   |
+===========+========================================================================+
| none      | 不读取 Albedo                                                          |
+-----------+------------------------------------------------------------------------+
| LDT       | 从 LDT 中产生的 LIS domain and parameter data file: 读取 Albedo 数据   |
+-----------+------------------------------------------------------------------------+
| ALMIPII   | 读取实时 ALMIP-II 反照率数据                                           |
+-----------+------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Albedo data source: LDT

``Max snow Albedo data source:``\ 指定运行中最大雪反照率数据的用法。
可选值有：

+---------+----------------------------------------------------------------------------+
| 值      | 描述                                                                       |
+=========+============================================================================+
| none    | 不读取 Albedo                                                              |
+---------+----------------------------------------------------------------------------+
| fixed   | 在“lis.config” 中使用修正后最大雪反照率                                    |
+---------+----------------------------------------------------------------------------+
|         | 此选项仅在 Noah-3.x LSMs 中可用                                            |
+---------+----------------------------------------------------------------------------+
| LDT     | 从 LDT 中产生的 LIS domain and parameter data file: 读取 Max snow Albedo   |
+---------+----------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Max snow albedo data source: LDT

``Greenness data source:``\ 指定运行中绿度数据的用法。 可选值有：

+-------------------+---------------------------------------------------------------------------+
| 值                | 描述                                                                      |
+===================+===========================================================================+
| none              | 不读取绿度组分                                                            |
+-------------------+---------------------------------------------------------------------------+
| LDT               | 从 LDT 中产生的 LIS domain and parameter data file: 读取 greenness 数据   |
+-------------------+---------------------------------------------------------------------------+
| “NESDIS weekly”   | 读取周度 NESDIS greenness组分                                             |
+-------------------+---------------------------------------------------------------------------+
| SPORT             | 读取 SPORT 绿度组分                                                       |
+-------------------+---------------------------------------------------------------------------+
| VIIRS             | 读取 SPORT 绿度组分                                                       |
+-------------------+---------------------------------------------------------------------------+
| ALMIPII           | 读取实时 ALMIP-II绿度数据                                                 |
+-------------------+---------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Greenness data source: LDT

``Roughness data source:``\ 指定运行中粗糙度数据的用法。 可选值有：

+-----------+---------------------------------------------------------------------------+
| 值        | 描述                                                                      |
+===========+===========================================================================+
| none      | 不读取粗糙度                                                              |
+-----------+---------------------------------------------------------------------------+
| LDT       | 从 LDT 中产生的 LIS domain and parameter data file: 读取 Roughness 数据   |
+-----------+---------------------------------------------------------------------------+
| ALMIPII   | 读取实时 ALMIP-II 粗糙度数据                                              |
+-----------+---------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Roughness data source: none

``Porosity data source:``\ 指定运行中土壤疏松度数据的用法。 可选值有：

+--------+--------------------------------------------------------------------------+
| 值     | 描述                                                                     |
+========+==========================================================================+
| none   | 不使用 soil porosity                                                     |
+--------+--------------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 porosity 数据   |
+--------+--------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Porosity data source: none

``Ksat data source:``\ 指定运行中渗透系数（水利传导系数）数据的用法。
可选值有：

+--------+----------------------------------------------------------------------------------------+
| 值     | 描述                                                                                   |
+========+========================================================================================+
| none   | 不读取 hydraulic conductivity                                                          |
+--------+----------------------------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 hydraulic conductivity 数据   |
+--------+----------------------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Ksat data source: none

``β parameter source:``\ 指定运行中 β 参数数据的用法。 可选值有：

+--------+-----------------------------------------------------------------------+
| 值     | 描述                                                                  |
+========+=======================================================================+
| none   | 不读取 b 参数                                                         |
+--------+-----------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 b 参数数据   |
+--------+-----------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    B parameter data source: none

``Quartz data source:`` 指定运行中石英数据的用法。 可选值有：

+--------+---------------------------------------------------------------------+
| 值     | 描述                                                                |
+========+=====================================================================+
| none   | 不读取石英数据                                                      |
+--------+---------------------------------------------------------------------+
| LDT    | 从 LDT 中产生的 LIS domain and parameter data file: 读取 石英数据   |
+--------+---------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Quartz data source: none

``Emissivity data source:`` 指定运行中发射率数据的用法。 可选值有：

+-----------+----------------------------------------------------------------------------+
| 值        | 描述                                                                       |
+===========+============================================================================+
| none      | 不读取发射率                                                               |
+-----------+----------------------------------------------------------------------------+
| LDT       | 从 LDT 中产生的 LIS domain and parameter data file: 读取 Emissivity 数据   |
+-----------+----------------------------------------------------------------------------+
| ALMIPII   | 读取实时 ALMIP-II 发射率数据                                               |
+-----------+----------------------------------------------------------------------------+

*lis.config* 输入示例

.. code:: shell

    Emissivity data source: none

9.8.2. TBOT lag
~~~~~~~~~~~~~~~

``TBOT lag skin temperature update option:``
指定是否将深层土壤温度调整为上一年的年平均表层温度和近期日平均表层温度的时间序列的加权平均值。可接受的值是：

+------+----------------------+
| 值   | 描述                 |
+======+======================+
| 0    | 不调整深层土壤温度   |
+------+----------------------+
| 1    | 调整深层土壤温度     |
+------+----------------------+

``TBOT skin temperature lag days:`` 指定延迟天数。

*lis.config* 输入示例

.. code:: shell

    TBOT lag skin temperature update option: 0 
    TBOT skin temperature lag days:          0

9.8.3. MODIS real-time LAI
~~~~~~~~~~~~~~~~~~~~~~~~~~

``MODIS RT LAI data directory:`` 指定 MODIS 实时叶面积指数文件位置。

*lis.config* 输入示例

.. code:: shell

    MODIS LAI data directory:

9.8.4. NESDIS weekly greenness fraction
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``NESDIS greenness data directory:`` 指定 NESDIS 周度绿度文件位置。

*lis.config* 输入示例

.. code:: shell

    NESDIS greenness data directory:

9.8.5. SPORT greenness fraction
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``SPORT greenness data directory:`` 指定SPORT 绿度文件位置。

``SPORT GVF use realtime mode:``
指定是否使用实时模式。当不使用实时模式时，LIS读取前一个和下一个 GVF
bookends 用于时间插值。使用实时模式时，LIS仅读取下一个 GVF bookends
以进行时间插值。可选值有：

+------+------------------+
| 值   | 描述             |
+======+==================+
| 0    | 不使用实时模式   |
+------+------------------+
| 1    | 使用实时模式     |
+------+------------------+

``SPORT GVF lower left lat:`` 指定 SPORT GVF
域的左下纬度。（柱面纬度/经度投影） ``SPORT GVF lower left lon:`` 指定
SPORT GVF 域的左下经度。（柱面纬度/经度投影）
``SPORT GVF upper right lat:`` 指定 SPORT GVF 域的右上纬度。
（柱面纬度/经度投影） ``SPORT GVF upper right lon:`` 指定 SPORT GVF
域的左下经度。 （柱面纬度/经度投影） ``SPORT GVF resolution (dx):`` 指定
SPORT GVF 域沿东西方向的分辨率。 ``SPORT GVF resolution (dy):`` 指定
SPORT GVF 域沿北南方向的分辨率。

*lis.config* 输入示例

.. code:: shell

    SPORT greenness data directory:  ./LISDATA/MODISNDVI/GVF_COMBINED_GLOBAL/gvf_SPORT_3KM 
    SPORT GVF use realtime mode:      1 
    SPORT GVF lower left lat:        -59.985 
    SPORT GVF lower left lon:       -179.985 
    SPORT GVF upper right lat:        89.985 
    SPORT GVF upper right lon:       179.985 
    SPORT GVF resolution (dx):         0.03 
    SPORT GVF resolution (dy):         0.03

9.8.6. VIIRS greenness fraction
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``VIIRS GVF use realtime mode:``
指定是否使用实时模式。当不使用实时模式时，LIS读取前一个和下一个 GVF
bookends 用于时间插值。使用实时模式时，LIS仅读取下一个 GVF bookends
以进行时间插值。可选值有：

+------+------------------+
| 值   | 描述             |
+======+==================+
| 0    | 不使用实时模式   |
+------+------------------+
| 1    | 使用实时模式     |
+------+------------------+

``VIIRS GVF lower left lat:`` 指定 VIIRS GVF
域的左下纬度。（柱面纬度/经度投影） ``VIIRS GVF lower left lon:`` 指定
VIIRS GVF 域的左下经度。（柱面纬度/经度投影）
``VIIRS GVF upper right lat:`` 指定 VIIRS GVF 域的右上纬度。
（柱面纬度/经度投影） ``VIIRS GVF upper right lon:`` 指定 VIIRS GVF
域的左下经度。 （柱面纬度/经度投影） ``VIIRS GVF resolution (dx):`` 指定
VIIRS GVF 域沿东西方向的分辨率。 ``VIIRS GVF resolution (dy):`` 指定
VIIRS GVF 域沿北南方向的分辨率。 ``VIIRS greenness data directory:``
指定 VIIRS 绿度文件的位置。 *lis.config* 输入示例

.. code:: shell

    VIIRS greenness data directory:  ./LISDATA/VIIRSGVF/NESDIS_GVF_LISREAL/gvf_VIIRS_4KM 
    VIIRS GVF use realtime mode:      1 
    VIIRS GVF lower left lat:        -89.982 
    VIIRS GVF lower left lon:       -179.982 
    VIIRS GVF upper right lat:        89.982 
    VIIRS GVF upper right lon:       179.982 
    VIIRS GVF resolution (dx):         0.036 
    VIIRS GVF resolution (dy):         0.036

9.9. 驱动
---------

9.9.1. GDAS
~~~~~~~~~~~

``GDAS forcing directory:`` 指定 GDAS 驱动数据文件的位置。

*lis.config* 输入示例

.. code:: shell

    GDAS forcing directory:            ./input/FORCING/GDAS/

9.9.2. GEOS
~~~~~~~~~~~

10. 指定输入驱动变量
====================

本节定义 LIS
的输入强制驱动变量的规范。此文件以空格分隔的列格式指定。每行包含以下条目：

``Short Name`` 驱动变量的简称

``Use option`` 确定是否包含此变量以便在 LIS 中使用，可选值有

+------+----------------+
| 值   | 描述           |
+======+================+
| 0    | 不包含此变量   |
+------+----------------+
| 1    | 包含此变量     |
+------+----------------+

``Number of vertical levels`` 与变量相关的垂直等级数量

``Units`` 指定变量的单位

请注意，这是输入驱动变量的完整列表，并非所有模型都使用所有这些变量。

请注意，在 forcing\_variables.txt 文件中列出并不保证该字段在 LIS
中可用。可用性取决于在lis.config
运行时配置文件中选择的基本驱动和补充驱动方案提供的支持。

.. code:: shell

    #short name select vlevels units
    Tair: 1 1 K # Near surface air temperature
    Qair: 1 1 kg/kg # Near surface specific humidity
    SWdown: 1 1 W/m2 # Incident shortwave radiation (total)
    SWdirect: 0 1 W/m2 # Incident shortwave radiation (direct)
    SWdiffuse: 0 1 W/m2 # Incident shortwave radiation (diffuse)
    LWdown: 1 1 W/m2 # Incident longwave radiation
    Wind_E: 1 1 W/m2 # Eastward wind
    Wind_N: 1 1 m/s # Northward wind
    Psurf: 1 1 Pa # Surface pressure
    Rainf: 1 1 kg/m2s # Rainfall rate
    Snowf: 0 1 kg/m2s # Snowfall rate
    CRainf: 1 1 kg/m2s # Convective rainfall rate
    Forc_Hgt: 0 1 m # Height of forcing variables
    Ch: 0 1 - # Surface exchange coefficient for heat
    Cm: 0 1 - # Surface exchange coefficient for momentum
    Q2sat: 0 1 - # Saturated mixing ratio
    Emiss: 0 1 - # Surface emissivity
    Cosz: 0 1 - # Cosine of zenith angle
    Albedo: 0 1 - # Surface albedo
    PARDR: 0 1 - # Photosynthetically Active Direct Radiation
    PARDF: 0 1 - # Photosynthetically Active Diffuse Radiation
    SWnet: 0 1 - # Net Shortwave Radiation at the Surface
    PET: 0 1 kg/m2s # Potential ET
    RefET: 0 1 kg/m2s # Reference ET
    CAPE: 0 1 J/kg # Convective Available Potential Energy
    LPressure: 0 1 Pa # Level pressure
    O3: 0 1 - # Ozone concentration
    Xice: 0 1 - # Sea ice mask
    QSFC: 0 1 kg/kg # Surface specific humidity
    CHS2: 0 1 - # 2m Surface Exchange Coefficient for Heat
    CQS2: 0 1 - # 2m Surface Exchange Coefficient for Moisture
    T2: 0 1 K # 2m Air Temperature
    Q2: 0 1 kg/kg # 2m Specific Humidity
    TH2: 0 1 K # 2m Potential Temperature
    TMN: 0 1 K # Soil Temperature at Lower Boundary
    Snowflag: 0 1 - # Snowflag
    Density: 0 1 kg/m3 # Atmospheric Density
    VaporPress: 0 1 Pa # Vapor Pressure
    VaporPressDeficit: 0 1 Pa # Vapor Pressure Deficit
    Wind: 0 1 m/s # Wind Speed

11. 指定模型输出变量
====================

本节定义了 LIS
模型输出的规范。此文件以空格分隔的列格式指定。每行包含以下条目：

``Short Name`` 指定符合 ALMA 的变量短名称。

``Use option`` 指定是否写入变量。可选值有

+------+----------------+
| 值   | 描述           |
+======+================+
| 0    | 不写入此变量   |
+------+----------------+
| 1    | 写入此变量     |
+------+----------------+

``Units:``
指定输出变量的所需单位。您必须检查源代码以确定每个输出变量可用的所有单位。

``Sign Convention:`` 指定变量被视为具有正值的方向。请注意，LIS
中的土地模型采用“传统方法”，其中所有变量在其主导方向上被认为是正的。即，降水和辐射对地表呈正向（向下），蒸发，显热和径流远离地表是正的。

可选值有

+-------+-------+--------------------------------------------+
| 值    |       | 描述                                       |
+=======+=======+============================================+
| -     |       | 无标记                                     |
+-------+-------+--------------------------------------------+
| UP    | DN    | 上或下（用于fluxes，降水）                 |
+-------+-------+--------------------------------------------+
| IN    | OUT   | 栅格像元的近或出（ 用于径流，基流 ）       |
+-------+-------+--------------------------------------------+
| INC   | DEC   | 增加或减少（用于储存项）                   |
+-------+-------+--------------------------------------------+
| S2L   | L2S   | 固体到液体和液体到固体（用于阶段改变项）   |
+-------+-------+--------------------------------------------+
| S2V   | V2S   | 固体到气体和气体到固体（用于阶段改变项）   |
+-------+-------+--------------------------------------------+
| E     | N     | 向东和向北（用于风力部分）                 |
+-------+-------+--------------------------------------------+

``Time Average option`` 确定如何在时间维处理变量。 可选值有

+------+------------------------+
| 值   | 描述                   |
+======+========================+
| 0    | 即时输出               |
+------+------------------------+
| 1    | 时间维平均输出         |
+------+------------------------+
| 2    | 即时和时间维平均输出   |
+------+------------------------+
| 3    | 累计输出               |
+------+------------------------+

``Min/Max option``
确定是否记录变量的最小值和最大值。对于给定的网格像元，最小值和最大值对应于在该输出间隔期间网格单元格中包含的所有子网格图块和集合的最小值和最大值。可选值有

+------+------------------------+
| 值   | 描述                   |
+======+========================+
| 0    | 不计算最小值和最大值   |
+------+------------------------+
| 1    | 计算最小值和最大值     |
+------+------------------------+

``Standard Deviation option``
确定是否记录变量值的标准偏差。对于给定的网格像元，标准偏差是从网格像元的平均值中对网格单元中包含的子网格块和集合的扩展的度量。可接受的值是：

+------+----------------+
| 值   | 描述           |
+======+================+
| 0    | 不计算标准差   |
+------+----------------+
| 1    | 计算标准差     |
+------+----------------+

``Number of vertical levels`` 指定与变量相关的垂直等级数量

``grid ID`` 指定用于变量网格 ID

``grib scale factor`` 指定当输出以 grib1 格式写入时用于变量的 grib
比例因子。

请注意，这是输出变量的完整列表。并非所有模型都支持所有这些变量。您必须检查源代码以验证要运行的模型是否支持您要编写的变量。
 footnotesize

.. code:: shell

    #short_name select? units signconv timeavg? min/max? std? vert.levels grib_id
    grib_scalefactor longname
    #Energy balance components
    Swnet: 1 W/m2 DN 1 0 0 1 111 10 # Net shortwave radiation (W/m2)
    Lwnet: 1 W/m2 DN 1 0 0 1 112 10 # Net longwave radiation (W/m2)
    Qle: 1 W/m2 UP 1 0 0 1 121 10 # Latent heat flux (W/m2)
    Qh: 1 W/m2 UP 1 0 0 1 122 10 # Sensible heat flux (W/m2)
    Qg: 1 W/m2 DN 1 0 0 1 155 10 # Ground heat flux (W/m2)
    Qf: 0 W/m2 S2L 1 0 0 1 229 10 # Energy of fusion (W/m2)
    Qv: 0 W/m2 S2V 1 0 0 1 198 10 # Energy of sublimation (W/m2)
    Qa: 0 W/m2 DN 1 0 0 1 136 10 # Advective energy (W/m2)
    Qtau: 0 N/m2 DN 1 0 0 1 172 10 # Momentum flux (N/m2)
    DelSurfHeat: 0 J/m2 INC 1 0 0 1 137 10 # Change in surface heat storage
    (J/m2)
    DelColdCont: 0 J/m2 INC 1 0 0 1 138 10 # Change in snow cold content (J/m2)
    BR: 0 - - 1 0 1 1 256 10 # Bowen ratio
    EF: 0 - - 1 0 1 1 256 10 # Evaporative fraction
    Rnet: 0 W/m2 DN 1 0 1 1 256 10 # Total net radiation
    #Water balance components
    Snowf: 1 kg/m2s DN 1 0 0 1 161 10000 # Snowfall rate (kg/m2s)
    Rainf: 1 kg/m2s DN 1 0 0 1 162 10000 # Rainfall rate (kg/m2s)
    RainfConv: 1 kg/m2s DN 1 0 0 1 63 10000 # Convective rainfall rate (kg/m2s)
    TotalPrecip: 1 kg/m2s DN 1 0 0 1 61 10000 # Total precipitation rate (kg/m2s)
    Evap: 1 kg/m2s UP 1 0 0 1 57 10000 # Total evapotranspiration (kg/m2s)
    176
    Qs: 1 kg/m2s OUT 1 0 0 1 235 10000 # Surface runoff (kg/m2s)
    Qrec: 0 kg/m2s IN 1 0 0 1 163 10000 # Recharge (kg/m2s)
    Qsb: 1 kg/m2s OUT 1 0 0 1 234 10000 # Subsurface runoff (kg/m2s)
    Qsm: 0 kg/m2s S2L 1 0 0 1 99 10000 # Snowmelt (kg/m2s)
    Qfz: 0 kg/m2s L2S 1 0 0 1 130 10000 # Refreezing of water in the
    snowpack (kg/m2s)
    Qst: 0 kg/m2s - 1 0 0 1 131 10000 # Snow throughfall (kg/m2s)
    DelSoilMoist: 0 kg/m2 INC 1 0 0 1 132 10000 # Change in soil moisture (kg/m2)
    DelSWE: 0 kg/m2 INC 1 0 0 1 133 1000 # Change in snow water equivalent
    (kg/m2)
    DelSurfStor: 0 kg/m2 INC 1 0 0 1 134 1000 # Change in surface water storage
    (kg/m2)
    DelIntercept: 0 kg/m2 INC 1 0 0 1 135 1000 # Change in interception storage
    (kg/m2)
    RHMin: 0 - - 1 0 0 1 52 10 # Minimum 2-meter relative humidity
    (-)
    Ch: 0 m/s - 1 0 0 1 208 10 # Surface exchange coefficient for
    heat
    Cm: 0 m/s - 1 0 0 1 252 10 # Surface exchange coefficient for
    momentum
    #Surface state variables
    SnowT: 0 K - 1 0 0 1 165 10 # Snow surface temperature (K)
    VegT: 0 K - 1 0 0 1 146 10 # Vegetation canopy temperature (K)
    BareSoilT: 0 K - 1 0 0 1 147 10 # Temperature of bare soil (K)
    AvgSurfT: 1 K - 1 0 0 1 148 10 # Average surface temperature (K)
    RadT: 0 K - 1 0 0 1 149 10 # Surface radiative temperature (K)
    Albedo: 1 - - 0 0 0 1 84 100 # Surface albedo (-)
    SWE: 1 kg/m2 - 0 0 0 1 65 1000 # Snow Water Equivalent (kg/m2)
    SWEVeg: 0 kg/m2 - 1 0 0 1 139 1000 # SWE intercepted by vegetation
    (kg/m2)
    SurfStor: 0 kg/m2 - 1 0 0 1 150 1000 # Surface water storage (kg/m2)
    #Subsurface state variables
    SoilMoist: 1 kg/m2 - 0 0 0 4 86 1000 # Average layer soil moisture
    (kg/m2)
    SoilTemp: 1 K - 0 0 0 4 85 1000 # Average layer soil temperature (K)
    SmLiqFrac: 0 - - 0 0 0 4 160 100 # Average layer fraction of liquid
    moisture (-)
    SmFrozFrac: 0 - - 0 0 0 4 140 100 # Average layer fraction of frozen
    moisture (-)
    SoilWet: 0 - - 0 0 0 1 144 100 # Total soil wetness (-)
    RelSMC: 0 m3/m3 - 0 0 0 4 141 1000 # Relative soil moisture
    RootTemp: 0 K - 0 0 0 1 142 1000 # Rootzone temperature (K)
    #Evaporation components
    PotEvap: 0 kg/m2s UP 1 0 0 1 145 1 # Potential evapotranspiration
    (kg/m2s)
    ECanop: 0 kg/m2s UP 1 0 0 1 200 1 # Interception evaporation (kg/m2s)
    TVeg: 0 kg/m2s UP 1 0 0 1 210 1 # Vegetation transpiration (kg/m2s)
    ESoil: 0 kg/m2s UP 1 0 0 1 199 1 # Bare soil evaporation (kg/m2s)
    177
    EWater: 0 kg/m2s UP 1 0 0 1 197 1 # Open water evaporation (kg/m2s)
    RootMoist: 0 kg/m2 - 0 0 0 1 171 1 # Root zone soil moisture (kg/m2)
    CanopInt: 0 kg/m2 - 0 0 0 1 223 1000 # Total canopy water storage (kg/m2)
    EvapSnow: 0 kg/m2s - 1 0 0 1 173 1000 # Snow evaporation (kg/m2s)
    SubSnow: 0 kg/m2s - 1 0 0 1 198 1000 # Snow sublimation (kg/m2s)
    SubSurf: 0 kg/m2s - 1 0 0 1 143 1000 # Sublimation of the snow free area
    (kg/m2s)
    ACond: 0 m/s - 1 0 0 1 179 100000 # Aerodynamic conductance
    CCond: 0 m/s - 1 0 0 1 181 100000 # Canopy conductance
    SoilET: 0 kg/m2 - 1 0 0 1 256 1 # Soil evaporation
    AResist: 0 s/m - 1 0 0 1 256 1 # Aerodynamic resistance
    #Other hydrologic variables
    WaterTableD: 0 m - 0 0 0 1 174 1 # Water table depth (m)
    TWS: 0 mm - 0 0 0 1 175 1 # Terrestrial water storage (mm)
    GWS: 0 mm - 0 0 0 1 176 1 # Ground water storage (mm)
    #Cold season processes
    Snowcover: 0 - - 0 0 0 1 238 100 # Snow cover (-)
    SAlbedo: 0 - - 0 0 0 1 184 1000 # Albedo of the snow-covered area (-
    )
    SnowTProf: 0 K - 0 0 0 1 239 1000 # Temperature of the snow pack (K)
    SnowDepth: 0 m - 0 0 0 1 66 1000 # Snow depth (m)
    SLiqFrac: 0 - - 0 0 0 1 185 1000 # Fraction of SWE in the liquid
    phase
    #Variables to compared against remote sensed data
    LWup: 0 W/m2 UP 1 0 0 1 212 1 # Longwave radiation up from the
    surface (W/m2)
    #Carbon variables
    GPP: 0 kg/m2s2 DN 1 0 0 1 256 1 # Gross Primary Production
    NPP: 0 kg/m2s2 DN 1 0 0 1 256 1 # Net Primary Production
    NEE: 0 kg/m2s2 UP 1 0 0 1 256 1 # Net Ecosystem Exchange
    AutoResp: 0 kg/m2s2 UP 1 0 0 1 256 1 # Autotrophic respiration
    HeteroResp: 0 kg/m2s2 UP 1 0 0 1 256 1 # Heterotrophic respiration
    LeafResp: 0 kg/m2s2 UP 1 0 0 1 256 1 # Leaf respiration
    TotSoilCarb: 0 kg/m2 - 1 0 0 1 256 1 # Total soil carbon
    TotLivBiom: 0 kg/m2 - 1 0 0 1 256 1 # Total living biomass
    #Forcings
    Wind_f: 1 m/s - 1 0 0 1 32 10 # Near surface wind (m/s)
    Rainf_f: 1 kg/m2s DN 1 0 0 1 162 1000 # Average rainfall rate
    Snowf_f: 0 kg/m2s DN 1 0 0 1 161 1000 # Average snowfall rate
    CRainf_f: 1 kg/m2 DN 1 0 0 1 63 1000 # Average convective rainfall rate
    Tair_f: 1 K - 1 0 0 1 11 10 # Near surface air temperature
    Qair_f: 1 kg/kg - 1 0 0 1 51 1000 # Near surface specific humidity
    Psurf_f: 1 Pa - 1 0 0 1 1 10 # Surface pressure
    SWdown_f: 1 W/m2 DN 1 0 0 1 204 10 # Surface incident shortwave
    radiation
    LWdown_f: 1 W/m2 DN 1 0 0 1 205 10 # Surface incident longwave
    178
    radiation
    PARDR_f: 0 W/m2 DN 1 0 0 1 256 10 # Surface incident PAR direct
    PARDF_f: 0 W/m2 DN 1 0 0 1 256 10 # Surface incident PAR diffuse
    #Additional forcings
    DirectSW_f: 0 W/m2 - 1 0 0 1 166 10 # Surface direct incident shortwave
    radiation
    DiffuseSW_f: 0 W/m2 - 1 0 0 1 167 10 # Surface diffuse incident shortwave
    radiation
    NWind_f: 0 m/s N 1 0 0 1 34 10 # Northward wind
    EWind_f: 0 m/s E 1 0 0 1 33 10 # Eastward wind
    FHeight_f: 0 m - 1 0 0 1 256 10 # Height of forcing variables
    Ch_f: 0 m/s - 1 0 0 1 208 10 # Surface exchange coefficient for
    heat
    Cm_f: 0 m/s - 1 0 0 1 252 10 # Surface exchange coefficient for
    momentum
    Emiss_f: 0 - - 1 0 0 1 256 10 # Surface emissivity
    MixRatio_f: 0 kg/kg - 1 0 0 1 53 10 # Surface mixing ratio
    CosZenith_f: 0 - - 1 0 0 1 256 10 # Cosine of zenith angle
    Albedo_f: 0 - - 1 0 0 1 84 10 # Surface albedo
    CAPE_f: 0 J/kg - 1 0 0 1 157 10 # Convective Available Potential
    Energy
    Z0brd: 0 m - 1 0 0 1 256 1 # Z0brd
    T2diag: 0 K - 1 0 0 1 256 1 # Diagnostic t2
    Q2diag: 0 kg/kg - 1 0 0 1 256 1 # Diagnostic q2
    Snowflag_f: 0 - - 1 0 0 1 256 1 # Snowflag
    Density_f: 0 kg/m3 - 1 0 0 1 256 1 # Atmospheric density
    VaporPress_f: 0 - - 1 0 0 1 256 1 # Vapor pressure
    VaporPressDeficit_f: 0 - - 1 0 0 1 256 1 # Vapor pressure deficit
    #Additional FEWSNET Forcings
    PET_f: 0 kg/m2s - 0 0 0 1 228 1000 # Average PET rate
    RefET_f: 0 kg/m2s - 0 0 0 1 256 1000 # Average RefET rate
    TotalPrecip_f: 0 kg/m2 DN 0 0 0 1 256 1000 # Total precipitation
    #Parameters
    Landmask: 0 - - 0 0 0 1 81 1 # Land mask (0 - Water, 1 - Land)
    Landcover: 0 - - 0 0 0 1 225 1 # Land cover
    Soiltype: 0 - - 0 0 0 1 224 1 # Soil type
    SandFrac: 0 - - 0 0 0 1 256 1 # Sand fraction
    ClayFrac: 0 - - 0 0 0 1 256 1 # Clay fraction
    SiltFrac: 0 - - 0 0 0 1 256 1 # Silt fraction
    Porosity: 0 - - 0 0 0 1 240 1 # Porosity
    Soilcolor: 0 - - 0 0 0 1 256 1 # Soil color
    Elevation: 0 m - 0 0 0 1 196 10 # Elevation
    Slope: 0 - - 0 0 0 1 222 10 # Slope
    LAI: 0 - - 0 0 0 1 182 100 # LAI
    SAI: 0 - - 0 0 0 1 256 100 # SAI
    Snfralbedo: 0 - - 0 0 0 1 184 100 # Snow fraction albedo
    Mxsnalbedo: 0 - - 0 0 0 1 159 100 # Maximum snow albedo
    Greenness: 0 - - 0 0 0 1 87 100 # Greenness
    179
    Roughness: 0 m - 0 0 0 1 83 10 # Roughness
    Tempbot: 0 K - 0 0 0 1 256 10 # Bottom soil temperature
    #Routing
    Streamflow: 0 m3/s - 1 0 0 1 256 10 # Streamflow
    #VIC PET output
    vic_pet_satsoil: 0 kg/m2s - 1 0 0 1 166 1 # Potential evap from saturated bare
    soil
    vic_pet_h2osurf: 0 kg/m2s - 1 0 0 1 166 1 # Potential evap from open water
    vic_pet_short: 0 kg/m2s - 1 0 0 1 166 1 # Potential evap (transpiration
    only) from short reference crop (grass)
    vic_pet_tall: 0 kg/m2s - 1 0 0 1 166 1 # Potential evap (transpiration
    only) from tall reference crop (alfalfa)
    vic_pet_natveg: 0 kg/m2s - 1 0 0 1 166 1 # Potential evap (transpiration
    only) from current vegetation and current canopy resistance
    vic_pet_vegnocr: 0 kg/m2s - 1 0 0 1 166 1 # Potential evap (transpiration
    only) from current vegetation and 0 canopy resistance
    #FLDAS-WRSI components
    SOS: 0 - - 0 0 0 1 0 10 # Start-of-season [in dekads]
    WRSI: 0 - - 0 0 0 1 0 10 # Water requirement satisfaction
    index [ratio]
    KF2: 0 % - 0 0 0 1 0 10 # Percent of Season [%]
    SumWR: 0 kg/m2 - 0 0 0 1 0 10 # Sum of Water Requirement [mm]
    SumET: 0 kg/m2 - 0 0 0 1 0 10 # Sum of Evapotranspiration [mm]
    SWI: 0 % - 0 0 0 1 0 10 # Soil Water Index [%]
    SOSa: 0 - - 0 0 0 1 0 10 # Start-of-season Anomaly [in
    dekads]
    TotalSurplusWater: 0 kg/m2 - 0 0 0 1 0 10 # Total surplus water [mm]
    MaxSurplusWater: 0 kg/m2 - 0 0 0 1 0 10 # Max surplus water experienced in 1
    dekad [mm]
    TotalWaterDeficit: 0 kg/m2 - 0 0 0 1 0 10 # Total water deficit [mm]
    MaxWaterDeficit: 0 kg/m2 - 0 0 0 1 0 10 # Max water deficit experienced in 1
    dekad [mm]
    TotalAETInitial: 0 kg/m2 - 0 0 0 1 0 10 # Actual evapotranspiration ~
    Initial stage [mm]
    TotalWRInitial: 0 kg/m2 - 0 0 0 1 0 10 # Water requirement ~ Initial stage
    [mm]
    TotalSurplusWaterInitial: 0 kg/m2 - 0 0 0 1 0 10 # Surplus water ~ Initial stage [mm]
    TotalWaterDeficitInitial: 0 kg/m2 - 0 0 0 1 0 10 # Water deficit ~ Initial stage [mm]
    TotalAETVeg: 0 kg/m2 - 0 0 0 1 0 10 # Actual evapotranspiration ~
    Vegetative stage [mm]
    TotalWRVeg: 0 kg/m2 - 0 0 0 1 0 10 # Water requirement ~ Vegetative
    stage [mm]
    TotalSurplusWaterVeg: 0 kg/m2 - 0 0 0 1 0 10 # Surplus water ~ Vegetative stage
    [mm]
    TotalWaterDeficitVeg: 0 kg/m2 - 0 0 0 1 0 10 # Water deficit ~ Vegetative stage
    [mm]
    TotalAETFlower: 0 kg/m2 - 0 0 0 1 0 10 # Actual evapotranspiration ~
    Flowering stage [mm]
    180
    TotalWRFlower: 0 kg/m2 - 0 0 0 1 0 10 # Water requirement ~ Flowering
    stage [mm]
    TotalSurplusWaterFlower: 0 kg/m2 - 0 0 0 1 0 10 # Surplus water ~ Flowering stage
    [mm]
    TotalWaterDeficitFlower: 0 kg/m2 - 0 0 0 1 0 10 # Water deficit ~ Flowering stage
    [mm]
    TotalAETRipe: 0 kg/m2 - 0 0 0 1 0 10 # Actual evapotranspiration ~
    Ripening stage [mm]
    TotalWRRipe: 0 kg/m2 - 0 0 0 1 0 10 # Water requirement ~ Ripening stage
    [mm]
    TotalSurplusWaterRipe: 0 kg/m2 - 0 0 0 1 0 10 # Surplus water ~ Ripening stage
    [mm]
    TotalWaterDeficitRipe: 0 kg/m2 - 0 0 0 1 0 10 # Water deficit ~ Ripening stage
    [mm]
    PermWiltDate: 0 - - 0 0 0 1 0 10 # Permanent wilting date [dekad]
    Wilting1: 0 - - 0 0 0 1 0 10 # First wilting date [dekad]
    Wilting2: 0 - - 0 0 0 1 0 10 # Second wilting date [dekad]
    WRSIa: 0 - - 0 0 0 1 0 10 # WRSI anomaly [-]
    growing_season: 0 - - 0 0 0 1 0 10 # Growing season [season-year]
    WHC: 0 kg/m2 - 0 0 0 1 0 10 # Water holding capacity; parameter
    [mm]
    LGP: 0 - - 0 0 0 1 0 10 # Length of growing period;
    parameter [dekad]
    WR_TimeStep: 0 kg/m2 - 0 0 0 1 0 10 # Water requirement per
    timestep(dekad) [mm]
    AET_TimeStep: 0 kg/m2 - 0 0 0 1 0 10 # Actual ET per timestep [mm]
    WRSI_TimeStep: 0 - - 0 0 0 1 0 10 # WRSI per timestep [-]
    SurplusWater_TimeStep: 0 kg/m2 - 0 0 0 1 0 10 # Surplus water per timestep [mm]
    #SacHTET specific output
    sac_tsint: 0 K - 0 0 0 1 256 10 # Soil temperature of inteneded layer
    sac_swint: 0 m3/m3 - 0 0 0 1 256 10 # Total volumetric soil moisture
    content of intended layer
    sac_swhint: 0 m3/m3 - 0 0 0 1 256 10 # Liquid volumetric soil moisture
    content of intended layer
    sac_frost: 0 - - 0 0 0 1 256 10 # Frost
    sac_uztwc: 0 mm - 0 0 0 1 256 10 # UZTWC
    sac_uzfwc: 0 mm - 0 0 0 1 256 10 # UZFWC
    sac_lztwc: 0 mm - 0 0 0 1 256 10 # LZTWC
    sac_lzfsc: 0 mm - 0 0 0 1 256 10 # LZFSC
    sac_lzfpc: 0 mm - 0 0 0 1 256 10 # LZFPC
    sac_adimpc: 0 mm - 0 0 0 1 256 10 # ADIMPC
    sac_uztwh: 0 mm - 0 0 0 1 256 10 # UZTWH
    sac_uzfwh: 0 mm - 0 0 0 1 256 10 # UZFWH
    sac_lztwh: 0 mm - 0 0 0 1 256 10 # LZTWH
    sac_lzfsh: 0 mm - 0 0 0 1 256 10 # LZFSH
    sac_lzfph: 0 mm - 0 0 0 1 256 10 # LZFPH
    #Snow17 specific output
    snow17_swe: 0 kg/m2 - 0 0 0 1 256 10 # SWE
    snow17_aeadj: 0 mm - 0 0 0 1 256 10 # AEADJ
    181
    snow17_neghs: 0 mm - 0 0 0 1 256 10 # NEGHS
    snow17_liqw: 0 kg/m2 - 0 0 0 1 256 10 # LIQW
    snow17_accmax: 0 mm - 0 0 0 1 256 10 # ACCMAX
    snow17_rmlt: 0 kg/m2 - 0 0 0 1 256 10 # RMLT
    Lake_Tsnow: 0 K - 0 0 0 1 256 10 # Lake temperature at the air snow
    interface
    Lake_Tice: 0 K - 0 0 0 1 256 10 # Lake temperature at the snow snow
    interface
    Lake_Tmnw: 0 K - 0 0 0 1 256 10 # Mean temperature of the water
    column
    Lake_Twml: 0 K - 0 0 0 1 256 10 # Lake temperature of the mixed layer
    Lake_Tbot: 0 K - 0 0 0 1 256 10 # Lake temperature at the water
    bottom
    Lake_Tb1: 0 K - 0 0 0 1 256 10 # Temperature at the bottom of upper
    layer of sediments
    Lake_CT: 0 - - 0 0 0 1 256 10 # Thermocline shape factor of lake
    Lake_Hice: 0 - - 0 0 0 1 256 10 # Ice thickness above lake
    Lake_Hml: 0 - - 0 0 0 1 256 10 # Thickness of mixed layer of lake
    Lake_Hb1: 0 - - 0 0 0 1 256 10 # Thickness of upper layer of bottom
    sediments
    Lake_Walbedo: 0 - - 0 0 0 1 256 10 # Water surface albedo over lake
    Lake_IceAlbedo: 0 - - 0 0 0 1 256 10 # Ice surface albedo over lake
    Lake_SnowAlbedo: 0 - - 0 0 0 1 256 10 # Snow surface albedo over lake
    Lake_UFRa: 0 - - 0 0 0 1 256 10 # Lake friction velocity in air
    Lake_UFRw: 0 - - 0 0 0 1 256 10 # Lake friction velocity in surface
    water
    Lake_WConv: 0 - - 0 0 0 1 256 10 # Lake convective velocity scale
    Lake_IW: 0 - - 0 0 0 1 256 10 # Lake radiation flux at the
    interface
    Lake_Qbot: 0 - - 0 0 0 1 256 10 # Lake heat flux across water
    sediment boundary
    RiverStor: 0 m3 - 0 0 0 1 256 10 # River water storage
    RiverDepth: 0 m - 0 0 0 1 256 10 # River depth
    RiverVelocity: 0 m/s - 0 0 0 1 256 10 # River flow velocity
    FloodQ: 0 m3/s - 0 0 0 1 256 10 # Floodplain water discharge
    FloodEvap: 0 m3 - 0 0 0 1 256 10 # Floodplain evaporation
    FloodStor: 0 m3 - 0 0 0 1 256 10 # Floodplain water storage
    FloodDepth: 0 m - 0 0 0 1 256 10 # Floodplain depth
    FloodVelocity: 0 m/s - 0 0 0 1 256 10 # Floodplain flow velocity
    FloodedFrac: 0 - - 0 0 0 1 256 10 # Flooded fraction
    FloodedArea: 0 m2 - 0 0 0 1 256 10 # Flooded area
    SurfElev: 0 m - 0 0 0 1 256 10 # Surface water elevation
    RunoffStor: 0 m3 - 0 0 0 1 256 10 # Runoff reservoir storage
    BaseflowStor: 0 m3 - 0 0 0 1 256 10 # Baseflow reservoir storage
    RTM emissivity: 0 - - 0 0 0 1 256 10 # RTM emissivity
    RTM Tb: 0 K - 0 0 0 1 256 10 # RTM brightness temperature
    RTM SoilMoist: 0 m3/m3 - 0 0 0 1 256 10 # RTM soil moisture
    Irrigated water: 0 kg/m2s - 0 0 0 1 256 10 # Irrigated water amount

