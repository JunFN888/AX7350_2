PCIe ROOT枚举测试
==================================

**实验VIvado工程为“pcie_root”。**

Vivado工程建立
--------------

创建新工程
~~~~~~~~~~

1) 新建一个工程名称为“pcie_root”,选择新建一个文件夹，点击“Next”

.. image:: images/17_media/image1.png
   :width: 5.34867in
   :height: 4.51054in

2) 工程类型选择“RTL Project”，同时选择“Do not specify sources at this
   time”，点击“Next”

.. image:: images/17_media/image2.png
   :width: 6.00417in
   :height: 5.06333in

3) “Parts”器件家族（Family）选择“Zynq-7000”，封装（Package）选择“ffg676”，然后选择“xc7z035ffg676-2”，点击“Next”

.. image:: images/17_media/image3.png
   :width: 6.00417in
   :height: 5.06333in

4) 点击“Finish”完成工程向导

创建block设计
~~~~~~~~~~~~~

1) 从Vivado流程导航窗口中点击“Create Block Design”

.. image:: images/17_media/image4.png
   :width: 5.50395in
   :height: 4.00129in

2) 给Block设计起名名字，这里保持“design_1”的默认名称，点击“OK”

.. image:: images/17_media/image5.png
   :width: 2.95677in
   :height: 2.22878in

3) 空白设计打开后，点击“Add IP”图标，在IP仓库中选择双击“ZYNQ7 Processing
   System”

.. image:: images/17_media/image6.png
   :width: 5.33294in
   :height: 3.62116in

4) ZYNQ PS block被添加到block设计中，点击“Run Block
   Automation”来配置目标硬件

.. image:: images/17_media/image7.png
   :width: 4.72003in
   :height: 2.54929in

5) 用默认设置，点击“OK”

.. image:: images/17_media/image8.png
   :width: 5.50434in
   :height: 3.44299in

6) 双击“processing_system7_0”配置ZYNQ PS

7) 在“PS-PL Configuration”选项卡中，使能HP Slave AXI接口“S AXI HP0
   interface”，HP端口可以让外部AXI Master接口高性能访问ZYNQ PS
   ddr3内存。

.. image:: images/17_media/image9.png
   :width: 6.00417in
   :height: 4.60618in

8) 在“Peripheral I/O Pins”选项中配置Bnak 1电平标准为“LVCMOS
   1.8V”，使能Quad SPI
   Flash，IO使用MIO1-MIO6，使能UART1，IO使用MIO12-MIO13

.. image:: images/17_media/image10.png
   :width: 6.00417in
   :height: 4.77388in

9) 使能Ethernet 0，IO使用MIO16-MIO27，使能USB0，IO使用MIO28-MIO39,

.. image:: images/17_media/image11.png
   :width: 6.00417in
   :height: 4.60618in

10) 使能Ethernet 0的MDIO，IO使用MIO52-MIO53，使能SD
    0，IO使用MIO40-MIO45，使能SD 1，IO使用MIO47-MIO51，使能I2C
    0，IO通过PL扩展，选择EMIO

.. image:: images/17_media/image12.png
   :width: 6.00417in
   :height: 4.60618in

11) SD 0的Card Detect选择MIO 10

.. image:: images/17_media/image13.png
   :width: 6.00417in
   :height: 4.46737in

12) 使能GPIO MIO，选择Ethernet PHY Reset为MIO7，USB PHY Reset为MIO8

.. image:: images/17_media/image14.png
   :width: 6.00417in
   :height: 4.60618in

13) 在DDR Configuration选择中选择Memory Part为“MT41J256M16 RE-125”

.. image:: images/17_media/image15.png
   :width: 6.00417in
   :height: 4.60618in

14) 在Interrupts选项中使能“Fabric
    Interrupts”，再使能IRQ_F2Q[15:0]，点击OK完成配置

.. image:: images/17_media/image16.png
   :width: 6.00417in
   :height: 4.60618in

添加AXI MM to PCIe bridge
~~~~~~~~~~~~~~~~~~~~~~~~~

1) 从IP 仓库添加 “AXI Memory Mapped to PCI Express” block 到设计中

.. image:: images/17_media/image17.png
   :width: 3.55501in
   :height: 3.11786in

2) 双击“axi_pcie_0”配置参数

3) 在“PCIE:Basics”配置选项中，端口类型选择“Root Port of PCI Express Root
   Complex”

.. image:: images/17_media/image18.png
   :width: 6.00417in
   :height: 4.26382in

4) 在“PCIE:Link Config”选项中，“Lane Width”选择X4，“Link speed”选择5
   GT/s

.. image:: images/17_media/image19.png
   :width: 6.00417in
   :height: 4.26382in

5) 在 “PCIE:ID” 选项中，“Class Code”填写 0x060400.
   这是为了以后在Linux下能正确的使用驱动程序。

.. image:: images/17_media/image20.png
   :width: 6.00417in
   :height: 4.26382in

6) 在“PCIE:BARS”项中, 设置BAR 0 类型为 “Memory” ，大小为 1
   Gigabytes，其他参数都保持默认，点击“OK”

.. image:: images/17_media/image21.png
   :width: 6.00417in
   :height: 4.26382in

7) 添加一个“Utility Buffer”到 block设计

.. image:: images/17_media/image22.png
   :width: 3.51163in
   :height: 3.1709in

8) 双击“util_ds_buf_0”，选择“C Buf
   Type”为“IBUFDSGTE”，这种类似是收发器专用参考差分时钟BUFFER，开发板上来自SI5338输出，频率为100Mhz

.. image:: images/17_media/image23.png
   :width: 5.46662in
   :height: 4.45871in

9) 连接 utility buffer 的输出“IBUF_OUT” 到 AXI-PCIe 模块的“REFCLK”输入。

.. image:: images/17_media/image24.png
   :width: 5.47228in
   :height: 2.14566in

10) 添加一个“Constant”模块到设计中

.. image:: images/17_media/image25.png
   :width: 3.31509in
   :height: 3.02237in

11) 双击“Constant”配置参数Const Val修改“0”

.. image:: images/17_media/image26.png
   :width: 5.25757in
   :height: 4.28821in

12) 把xlconstant_0输出连接到axi_pcie_0的输入INTX_MSI_Request

.. image:: images/17_media/image27.png
   :width: 5.04106in
   :height: 2.05602in

添加复位模块
~~~~~~~~~~~~

设计中有2个时钟，都是来自AXI-PCIe
bridge模块的输出，分别为“axi_aclk_out”、“axi_ctl_aclk_out”，一个用于PCIe数据传输，一个用于模块的寄存器控制，所以要添加2个复位模块。

1) 添加“Processor System Reset”到设计中，默认名称为“proc_sys_reset_0”

.. image:: images/17_media/image28.png
   :width: 2.99372in
   :height: 1.67109in

2) 把“axi_pcie_0”模块的输出“axi_ctl_aclk_out”连接到“proc_sys_reset_0”模块的输入“slowest_sync_clk”,
   把“axi_pcie_0”模块的输出“mmcm_lock”连接到“proc_sys_reset_0”模块的输入“dcm_locked”,把模块“processing_system7_0”的输出“FCLK_RESET0_N”连接到模块“proc_sys_reset_0”的输入“ext_reset_in”。

.. image:: images/17_media/image29.png
   :width: 5.07839in
   :height: 2.70182in

3) 添加“Processor System Reset”到设计中，默认名称为“proc_sys_reset_1”

4) 把“axi_pcie_0”模块的输出“axi_aclk_out”连接到“proc_sys_reset_1”模块的输入“slowest_sync_clk”,
   把“axi_pcie_0”模块的输出“mmcm_lock”连接到“proc_sys_reset_1”模块的输入“dcm_locked”,把模块“processing_system7_0”的输出“FCLK_RESET0_N”连接到模块“proc_sys_reset_1”的输入“ext_reset_in”。

.. image:: images/17_media/image30.png
   :width: 6.00417in
   :height: 3.42779in

添加DMA模块
~~~~~~~~~~~

1) 添加一个“AXI Central Direct Memory Access”到设计中

.. image:: images/17_media/image31.png
   :width: 3.72771in
   :height: 3.28921in

2) 双击“axi_cdma_0”打开配置窗口，“Write/Read Data
   Width”选择128，不要勾选“Enable Scatter Gather”

.. image:: images/17_media/image32.png
   :width: 5.19038in
   :height: 4.2334in

连接中断
~~~~~~~~

1) 添加一个“Concat”到设计中

.. image:: images/17_media/image33.png
   :width: 3.07112in
   :height: 3.35247in

2) 连接axi_pcie_0模块的中断到xlconcat_0模块的In0端口，连接axi_cdma_0模块的中断到xlconcat_0模块的In1端口，连接xlconcat_0模式的输出dout到ZYNQ
   PS处理器IRQ_F2P端口。

.. image:: images/17_media/image34.png
   :width: 6.00417in
   :height: 3.29488in

添加AXI互联模块
~~~~~~~~~~~~~~~

1) 添加一个“AXI
   Interconnect”模块到设计中，默认名称为“axi_interconnect_0”

.. image:: images/17_media/image35.png
   :width: 3.34726in
   :height: 3.39804in

2) 双击“axi_interconnect_0”，配置参数修改为2个Slave端口，1个Master端口

.. image:: images/17_media/image36.png
   :width: 6.00417in
   :height: 4.60618in

3) 连接“axi_interconnect_0”模块的M00_AXI端口到ZYNQ
   PS处理器的S_AXI_HP0端口

.. image:: images/17_media/image37.png
   :width: 5.03359in
   :height: 2.78723in

4) 添加一个“AXI
   Interconnect”模块到设计中，默认名称为“axi_interconnect_1”

5) 双击“axi_interconnect_1”，配置参数修改为2个Slave端口，3个Master端口

..

   .. image:: images/17_media/image38.png
      :width: 5.35984in
      :height: 4.11188in

6) 连接 “axi_interconnect_1”模块的“M00_AXI” 端口到AXI-PCIe模块的
   “S_AXI”端口，连接 “axi_interconnect_1”模块的“M01_AXI”
   端口到AXI-PCIe模块的 “S_AXI_CTL”端口，连接
   “axi_interconnect_1”模块的“M02_AXI” 端口到CDMA模块的 “S_AXI_LITE”端口

.. image:: images/17_media/image39.png
   :width: 5.08586in
   :height: 3.69223in

7) 添加一个“AXI
   Interconnect”模块到设计中，默认名称为“axi_interconnect_2”

8) 连接 “axi_interconnect_2”模块的“M00_AXI”端口到
   “axi_interconnect_0”模块的“S01_AXI” 端口，连接
   “axi_interconnect_2”模块的“M01_AXI”端口到
   “axi_interconnect_1”模块的“S01_AXI” 端口，连接
   “axi_interconnect_2”模块的“S00_AXI”端口到 “axi_cdma_0”模块的“M_AXI”
   端口

.. image:: images/17_media/image40.png
   :width: 5.12934in
   :height: 3.06103in

连接时钟
~~~~~~~~

1) 连接 “axi_aclk_out”时钟到Zynq PS的输入 “M_AXI_GP0_ACLK” 和
   “S_AXI_HP0_ACLK”

.. image:: images/17_media/image41.png
   :width: 6.00417in
   :height: 2.20234in

2) 连接 “axi_aclk_out”时钟到“axi_cdma_0”的输入 “m_axi_aclk” 和
   “s_axi_lite_aclk”

.. image:: images/17_media/image42.png
   :width: 6.00417in
   :height: 2.27949in

3) 连接“axi_interconnect_0”所有时钟到“axi_aclk_out”

.. image:: images/17_media/image43.png
   :width: 4.79518in
   :height: 3.34955in

4) 连接“axi_interconnect_1”除了“M01_ACLK”的所有时钟到“axi_aclk_out”

.. image:: images/17_media/image44.png
   :width: 5.37804in
   :height: 3.31087in

5) 连接“axi_interconnect_2”所有时钟到“axi_aclk_out”

.. image:: images/17_media/image45.png
   :width: 4.83948in
   :height: 2.67546in

6) 连接“axi_interconnect_1”的时钟“M01_ACLK”到“axi_ctl_aclk_out”

.. image:: images/17_media/image46.png
   :width: 4.3798in
   :height: 2.88369in

连接复位信号
~~~~~~~~~~~~

1) 连接复位模块“proc_sys_reset_0”的输出“peripheral_aresetn”到AXI互联模块“axi_interconnect_1”的复位输入“M01_ARESETN”。

.. image:: images/17_media/image47.png
   :width: 5.31739in
   :height: 2.44647in

2) 连接复位模块“proc_sys_reset_1”的输出“peripheral_aresetn”到AXI互联模块“axi_interconnect_1”的其他复位输入，连接到“axi_interconnect_0”、“axi_interconnect_2”的全部复位输入。

.. image:: images/17_media/image48.png
   :width: 5.04106in
   :height: 4.25425in

其他连接
~~~~~~~~

1) 连接“axi_pcie_0”的“M_AXI”到“axi_interconnect_0”的“S00_AXI”

.. image:: images/17_media/image49.png
   :width: 6.00417in
   :height: 2.81511in

2) 连接ZYNQ处理器的“M_AXI_GP0”到“axi_interconnect_1”的“S00_AXI”

.. image:: images/17_media/image50.png
   :width: 6.00417in
   :height: 2.83847in

端口设置
~~~~~~~~

1) util_ds_buf_0差分时钟输入，右键“Make External”

.. image:: images/17_media/image51.png
   :width: 3.95692in
   :height: 1.91175in

.. image:: images/17_media/image52.png
   :width: 4.06123in
   :height: 3.08109in

2) 修改名称为“pcie_ref”

.. image:: images/17_media/image53.png
   :width: 5.17545in
   :height: 2.96947in

3) 同样扩展出“axi_pcie_0”的“pcie_7x_mgt”端口，修改名称

.. image:: images/17_media/image54.png
   :width: 5.57861in
   :height: 2.54484in

4) 将IIC_0端口引出，端口名为“si5338_iic”，用于配置SI5338

.. image:: images/17_media/image55.png
   :width: 6.00417in
   :height: 2.16758in

5) 选择复位模块“proc_sys_reset_0”的输出“peripheral_aresetn”，右键然后选择“Create
   Port...”

.. image:: images/17_media/image56.png
   :width: 4.24539in
   :height: 5.22939in

6) 端口名称填写“pcie_rstn”

.. image:: images/17_media/image57.png
   :width: 3.39286in
   :height: 3.02966in

地址分配
~~~~~~~~

7) 在“Address Editor”窗口点击自动分配图标

.. image:: images/17_media/image58.png
   :width: 5.0112in
   :height: 3.62518in

8) 出现一个警告

.. image:: images/17_media/image59.png
   :width: 4.19748in
   :height: 2.63282in

9) 把axi_pcie_0的BAR0地址范围改为256M后，重新点击自动分配

.. image:: images/17_media/image60.png
   :width: 5.17277in
   :height: 2.60052in

10) 重新分配以后的地址，然后保存block设计

.. image:: images/17_media/image61.png
   :width: 5.17108in
   :height: 3.18428in

创建HDL封装
~~~~~~~~~~~

1) 在“Sources”选项卡中选择Block设计右键，然后“Create HDL wrapper”

.. image:: images/17_media/image62.png
   :width: 5.42929in
   :height: 3.07511in

2) 点击“OK”

.. image:: images/17_media/image63.png
   :width: 4.81499in
   :height: 2.78107in

添加xdc约束
~~~~~~~~~~~

1) 在约束选项下点击“Add Sources”，在弹出的窗口选择“Add or create
   constraints”

.. image:: images/17_media/image64.png
   :width: 6.00417in
   :height: 3.26265in

2) 点击“Create”，名称填写pcie，然后点击“Finish”

.. image:: images/17_media/image65.png
   :width: 6.00417in
   :height: 4.05452in

3) 修改xdc文件，文件内容不再给出，在开发板给的工程里可以找到（pcie_root/pcie_root.srcs/constrs_1/new/pcie.xdc）。

.. image:: images/17_media/image66.png
   :width: 6.00417in
   :height: 4.01553in

关键步骤
~~~~~~~~

pcie模块会自动带上管脚约束信息，但是和我们的硬件设计是不相符的，这个时候需要我们把自带的xdc文件添加不使用属性。

1) 在模块设计“design_1_i”右键选择“Generate Output Products...”

.. image:: images/17_media/image67.png
   :width: 4.26174in
   :height: 3.21809in

2) 在“IP
   Sources”选项卡中搜索xdc文件，axi_pcie_X0Y0.xdc就是我们要找的文件

.. image:: images/17_media/image68.png
   :width: 3.9366in
   :height: 3.65483in

3) 选择这个文件然后右键“Disable File”

.. image:: images/17_media/image69.png
   :width: 4.38947in
   :height: 3.73922in

4) 编译生成bit文件

.. image:: images/17_media/image70.png
   :width: 4.80429in
   :height: 6.39638in

Vitis下载调试
-------------

1) 导出硬件

.. image:: images/17_media/image71.png
   :width: 2.33194in
   :height: 2.83611in

2) 选择包含bit文件

.. image:: images/17_media/image72.png
   :width: 3.75625in
   :height: 3.18333in

3) 运行Vitis

.. image:: images/17_media/image73.png
   :width: 0.82292in
   :height: 0.79167in

4) 新建一个BSP

.. image:: images/17_media/image74.png
   :width: 5.99792in
   :height: 1.68889in

5) 保持默认的工程名，“Board Support Package OS”选择“standalone”

.. image:: images/17_media/image75.png
   :width: 6.00208in
   :height: 4.09583in

6) 保持默认配置，选择“OK”

.. image:: images/17_media/image76.png
   :width: 5.99653in
   :height: 4.04444in

7) 点击“Board Support Package”/Modify BSP
   Setting,在“axi_pcie_0”这一行选择“Import Examples”，导入一个例程

.. image:: images/17_media/image77.png
   :width: 5.99514in
   :height: 3.69306in

8) 选择“xaxipcie_rc_enumerate_example”

.. image:: images/17_media/image78.png
   :width: 5.99792in
   :height: 3.84375in

9) 复制文件用于si5338的配置，相关文件可以到开发板例程的Vitis目录找到

.. image:: images/17_media/image79.png
   :width: 5.99861in
   :height: 2.38889in

10) 在Vitis的工程目录选择src目录，右键“Paste”把上面复制的三个文件粘贴到src目录

.. image:: images/17_media/image80.png
   :width: 3.77292in
   :height: 4.59653in

11) 修改“xaxipcie_rc_enumerate_example.c”文件，添加si5338的相关配置，先添加头文件

.. image:: images/17_media/image81.png
   :width: 6.00347in
   :height: 4.03125in

12) 在PcieInitRootComplex函数中添加si5338的配置,保存编译。

.. image:: images/17_media/image82.png
   :width: 4.37292in
   :height: 4.31181in

13) 打开运行配置

.. image:: images/17_media/image83.png
   :width: 5.99583in
   :height: 4.71875in

14) 选择“Single Application Debug”双击

.. image:: images/17_media/image84.png
   :width: 6.00069in
   :height: 3.66181in

15) 在PCIe
    插槽插入PCIe设备，例如SSD，网卡等，连接串口调试终端，选择复位整个系统并编程FPGA，开发板上电后点击“Run”

.. image:: images/17_media/image85.png
   :width: 5.99931in
   :height: 3.65972in

16) 串口终端软件输入了一些信息，包括Si5338配置，PCIe设备的一些信息

.. image:: images/17_media/image86.png
   :width: 5.10002in
   :height: 4.4095in

实验总结
--------

本实验建立了PCIe
Root的Vivado工程，然后通过Vitis裸机程序测试了PCIe枚举设备的功能，为我们PCIe
Root应用打下基础，后面教程我们会通过Linux来使用PCIe Root功能。


*ZYNQ-7000开发平台 FPGA教程*    - `Alinx官方网站 <http://www.alinx.com>`_