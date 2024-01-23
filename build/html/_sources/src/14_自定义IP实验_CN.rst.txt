自定义IP实验
===============================

**实验VIvado工程为“custom_pwm_ip”。**

Xilinx官方为大家提供了很多IP核，在Vivado的IP Catalog中可以查看这些IP核，
用户在构建自己的系统中，不可能只使用Xilinx官方的免费IP核，很多时候需要创建属于自己的用户IP核，创建自己的IP核有很多好处，例如系统设计定制化；设计复用，可以在在IP核中加入license,
有偿提供给别人使用；简化系统设计和缩短设计时间。用ZYNQ系统设计IP核，最常用的就是使用AXI总线将PS同PL部分的IP核连接起来。本实验将为大家介绍如何在Vivado中构建AXI总线类型的IP核，此IP核用来产生一个PWM，用这个控制开发板上的LED，做一个呼吸灯的效果。

PWM介绍
-------

我们经常使用PWM来控制LED，蜂鸣器等，通过调节脉冲的占空比来调节LED的亮度。

在其他开发板中我们使用过的一个pwm模块如下：

+-----------------------------------------------------------------------+
|                                                                       |
| // //                                                                 |
|                                                                       |
| // //                                                                 |
|                                                                       |
| // Author: meisq //                                                   |
|                                                                       |
| // msq@qq.com //                                                      |
|                                                                       |
| // ALINX(shanghai) Technology Co.,Ltd //                              |
|                                                                       |
| // heijin //                                                          |
|                                                                       |
| // WEB: *http://www.alinx.cn/* //                                     |
|                                                                       |
| // BBS: *http://www.heijin.org/* //                                   |
|                                                                       |
| // //                                                                 |
|                                                                       |
| // Copyright (c) 2017,ALINX(shanghai) Technology Co.,Ltd //           |
|                                                                       |
| // All rights reserved //                                             |
|                                                                       |
| // //                                                                 |
|                                                                       |
| // This source file may be used and distributed without restriction   |
| provided //                                                           |
|                                                                       |
| // that this copyright statement is not removed from the file and     |
| that any //                                                           |
|                                                                       |
| // derivative work contains the original copyright notice and the     |
| associated //                                                         |
|                                                                       |
| // disclaimer. //                                                     |
|                                                                       |
| // //                                                                 |
|                                                                       |
|                                                                       |
| // Description: pwm model                                             |
|                                                                       |
| // pwm out period = frequency(pwm_out) \* (2 \*\* N) /                |
| frequency(clk);                                                       |
|                                                                       |
| //                                                                    |
|                                                                       |
| // Revision History:                                                  |
|                                                                       |
| // Date By Revision Change Description                                |
|                                                                       |
| //-----------                                                         |
|                                                                       |
| // 2017/5/3 meisq 1.0 Original                                        |
|                                                                       |
| \`timescale 1ns **/** 1ps                                             |
|                                                                       |
| **module** ax_pwm                                                     |
|                                                                       |
| **#(**                                                                |
|                                                                       |
| **parameter** N **=** 32 //pwm bit width                              |
|                                                                       |
| **)**                                                                 |
|                                                                       |
| **(**                                                                 |
|                                                                       |
| **input** clk\ **,**                                                  |
|                                                                       |
| **input** rst\ **,**                                                  |
|                                                                       |
| **input[**\ N **-** 1\ **:**\ 0\ **]**\ period\ **,**                 |
|                                                                       |
| **input[**\ N **-** 1\ **:**\ 0\ **]**\ duty\ **,**                   |
|                                                                       |
| **output** pwm_out                                                    |
|                                                                       |
| **);**                                                                |
|                                                                       |
| **reg[**\ N **-** 1\ **:**\ 0\ **]** period_r\ **;**                  |
|                                                                       |
| **reg[**\ N **-** 1\ **:**\ 0\ **]** duty_r\ **;**                    |
|                                                                       |
| **reg[**\ N **-** 1\ **:**\ 0\ **]** period_cnt\ **;**                |
|                                                                       |
| **reg** pwm_r\ **;**                                                  |
|                                                                       |
| **assign** pwm_out **=** pwm_r\ **;**                                 |
|                                                                       |
| **always@(posedge** clk **or** **posedge** rst\ **)**                 |
|                                                                       |
| **begin**                                                             |
|                                                                       |
| **if(**\ rst\ **==**\ 1\ **)**                                        |
|                                                                       |
| **begin**                                                             |
|                                                                       |
| period_r **<=** **{** N **{**\ 1'b0\ **}** **};**                     |
|                                                                       |
| duty_r **<=** **{** N **{**\ 1'b0\ **}** **};**                       |
|                                                                       |
| **end**                                                               |
|                                                                       |
| **else**                                                              |
|                                                                       |
| **begin**                                                             |
|                                                                       |
| period_r **<=** period\ **;**                                         |
|                                                                       |
| duty_r **<=** duty\ **;**                                             |
|                                                                       |
| **end**                                                               |
|                                                                       |
| **end**                                                               |
|                                                                       |
| **always@(posedge** clk **or** **posedge** rst\ **)**                 |
|                                                                       |
| **begin**                                                             |
|                                                                       |
| **if(**\ rst\ **==**\ 1\ **)**                                        |
|                                                                       |
| period_cnt **<=** **{** N **{**\ 1'b0\ **}** **};**                   |
|                                                                       |
| **else**                                                              |
|                                                                       |
| period_cnt **<=** period_cnt **+** period_r\ **;**                    |
|                                                                       |
| **end**                                                               |
|                                                                       |
| **always@(posedge** clk **or** **posedge** rst\ **)**                 |
|                                                                       |
| **begin**                                                             |
|                                                                       |
| **if(**\ rst\ **==**\ 1\ **)**                                        |
|                                                                       |
| **begin**                                                             |
|                                                                       |
| pwm_r **<=** 1'b0\ **;**                                              |
|                                                                       |
| **end**                                                               |
|                                                                       |
| **else**                                                              |
|                                                                       |
| **begin**                                                             |
|                                                                       |
| **if(**\ period_cnt **>=** duty_r\ **)**                              |
|                                                                       |
| pwm_r **<=** 1'b1\ **;**                                              |
|                                                                       |
| **else**                                                              |
|                                                                       |
| pwm_r **<=** 1'b0\ **;**                                              |
|                                                                       |
| **end**                                                               |
|                                                                       |
| **end**                                                               |
|                                                                       |
| **endmodule**                                                         |
+-----------------------------------------------------------------------+

可以看到这个PWM模块需要2个参数“period”、“duty”来控制频率和占空比，我们需要设计一些寄存器来控制这些参数，这里需要使用AXI总线，PS通过AXI总线来读写寄存器。

Vivado工程建立
--------------

创建一个vivado工程
~~~~~~~~~~~~~~~~~~

创建一个名为“custom_pwm_ip”工程，添加zynq
PS系统，并配置参数，具体方法可以参考前面方法

.. image:: images/14_media/image1.png
   :width: 4.81997in
   :height: 2.75348in

创建自定义IP
~~~~~~~~~~~~

1) 点击菜单“Tools->Create and Package IP...”

.. image:: images/14_media/image2.png
   :width: 4.68395in
   :height: 2.61966in

2) 选择“Next”

.. image:: images/14_media/image3.png
   :width: 5.10947in
   :height: 3.7946in

3) 选择创建一个新的AXI4设备

.. image:: images/14_media/image4.png
   :width: 4.94255in
   :height: 3.67063in

4) 名称填写“ax_pwm”,描述填写“alinx pwm”，然后选择一个合适的位置用来放IP

.. image:: images/14_media/image5.png
   :width: 5.03861in
   :height: 3.74197in

5) 下面参数可以指定接口类型、寄存器数量等，这里不需要修改，使用AXI Lite
      Slave接口，4个寄存器。

.. image:: images/14_media/image6.png
   :width: 5.10266in
   :height: 3.78953in

6) 点击“Finish”完成IP的创建

.. image:: images/14_media/image7.png
   :width: 5.33584in
   :height: 3.96271in

7) 在“IP Catalog”中可以看到刚才创建的IP

.. image:: images/14_media/image8.png
   :width: 6.00417in
   :height: 4.02901in

8) 这个时候的IP只有简单的寄存器读写功能，我们需要修改IP，选择IP，右键“Edit
      in IP Packager”

.. image:: images/14_media/image9.png
   :width: 3.42927in
   :height: 4.15223in

9) 这是弹出一个对话框，可以填写工程名称和路径，这里默认，点击“OK”

.. image:: images/14_media/image10.png
   :width: 4.28424in
   :height: 1.8595in

10) Vivado打开了一个新的工程

.. image:: images/14_media/image11.png
   :width: 6.00417in
   :height: 3.3287in

11) 添加PWM功能的核心代码

.. image:: images/14_media/image12.png
   :width: 6.00417in
   :height: 3.21788in

12) 添加代码时选择复制代码到IP目录

.. image:: images/14_media/image13.png
   :width: 6.00417in
   :height: 4.05877in

13) 修改“ax_pwm_v1_0.v”，添加一个pwm输出端口

.. image:: images/14_media/image14.png
   :width: 5.86014in
   :height: 3.33171in

14) 修改“ax_pwm_v1_0.v”，在例化“ax_pwm_V1_0_S00_AXI”,中添加pwm端口的例化

.. image:: images/14_media/image15.png
   :width: 6.00417in
   :height: 3.26159in

15) 修改“ax_pwm_v1_0_s00_AXI.v”文件，添加pwm端口，这个文件是实现AXI4
       Lite Slave的核心代码

.. image:: images/14_media/image16.png
   :width: 6.00417in
   :height: 3.59503in

16) 修改“ax_pwm_v1_0_s00_AXI.v”文件，例化pwm核心功能代码，将寄存器slv_reg0和slv_reg1用于pwm模块的参数控制。

.. image:: images/14_media/image17.png
   :width: 6.00417in
   :height: 3.56499in

17) 双击“component.xml”文件

.. image:: images/14_media/image18.png
   :width: 6.00417in
   :height: 3.52954in

18) 在“File Groups”选项中点击“Merge changers from File Groups Wizard”

.. image:: images/14_media/image19.png
   :width: 6.00417in
   :height: 3.51577in

19) 在“Customization Parameters”选项中点击“Merge changes form
       Customization Parameters Wizard”

.. image:: images/14_media/image20.png
   :width: 6.00417in
   :height: 3.29642in

20) 点击“Re-Package IP”完成IP的修改

.. image:: images/14_media/image21.png
   :width: 6.00417in
   :height: 4.11733in

添加自定义IP到工程
~~~~~~~~~~~~~~~~~~

1) 搜索“pwm”，添加“ax_pwm_v1.0”

.. image:: images/14_media/image22.png
   :width: 4.44296in
   :height: 3.65436in

2) 点击“Run Block Automation”

.. image:: images/14_media/image23.png
   :width: 5.48537in
   :height: 2.51125in

3) 点击“Run Connection Automation”

.. image:: images/14_media/image24.png
   :width: 4.792in
   :height: 2.62563in

4) 导出pwm端口

.. image:: images/14_media/image25.png
   :width: 4.31421in
   :height: 3.34801in

.. image:: images/14_media/image26.png
   :width: 6.00417in
   :height: 2.27303in

5) 创建HDL文件

.. image:: images/14_media/image27.png
   :width: 4.92708in
   :height: 4.23785in

6) 添加xdc文件分配管脚，把pwm_0输出端口分配给PL_LED1，做一个呼吸灯

+-----------------------------------------------------------------------+
| set_property IOSTANDARD LVCMOS15 [get_ports pwm_0]                    |
|                                                                       |
| set_property PACKAGE_PIN F5 [get_ports pwm_0]                         |
+-----------------------------------------------------------------------+

1) 编译生成bit文件

Vitis软件编写调试
-----------------

1) 导出硬件

.. image:: images/14_media/image28.png
   :width: 2.33194in
   :height: 2.83611in

.. image:: images/14_media/image29.png
   :width: 3.30764in
   :height: 2.80347in

2) 启动Vitis，新建APP，模板选择“Hello World”

..

   .. image:: images/14_media/image30.png
      :alt: C:/Users/Administrator/Desktop/vivado_2023.1/AX7010_2023.1/7010_S2文档/images/images_1/image46.pngimage46
      :width: 0.38819in
      :height: 0.38958in

   .. image:: images/14_media/image31.png
      :width: 2.39306in
      :height: 1.7375in

3) 前面的例都是使用xilinx的IP，xilinx大多都提供一套API，对于这个自定义IP，我们需要自己开发，先看看APP的目录下的资源，可以找到一个ax_pwm.h的文件，这个文件里包含里对自定义IP寄存器的读写宏定义

.. image:: images/14_media/image32.png
   :width: 6.00417in
   :height: 2.92986in

4) 在bsp里找到“xparameters.h”文件，这个非常重要的文件，里面找到了自定IP的寄存器基地址，可以找到自定义IP的基地址。

.. image:: images/14_media/image33.png
   :width: 3.99522in
   :height: 1.79071in

5) 有个寄存器读写宏和自定义IP的基地址，我们开始编写代码，测试自定义IP，我们先通过写寄存器AX_PWM_S00_AXI_SLV_REG0_OFFSET，控制PWM输出频率，然后通过写寄存器AX_PWM_S00_AXI_SLV_REG1_OFFSET控制PWM输出的占空比。

+-----------------------------------------------------------------------+
| **#include** <stdio.h>                                                |
|                                                                       |
| **#include** "platform.h"                                             |
|                                                                       |
| **#include** "xil_printf.h"                                           |
|                                                                       |
| **#include** "ax_pwm.h"                                               |
|                                                                       |
| **#include** "xil_io.h"                                               |
|                                                                       |
| **#include** "xparameters.h"                                          |
|                                                                       |
| **#include** "sleep.h"                                                |
|                                                                       |
| **unsigned** **int** duty;                                            |
|                                                                       |
| **int** **main**\ ()                                                  |
|                                                                       |
| {                                                                     |
|                                                                       |
| init_platform();                                                      |
|                                                                       |
| print("Hello World\\n\\r");                                           |
|                                                                       |
| //*pwm* out period = frequency(pwm_out) \* (2 \*\* N) /               |
| frequency(*clk*);                                                     |
|                                                                       |
| AX_PWM_mWriteReg(XPAR_AX_PWM_0_S00_AXI_BASEADDR,                      |
| AX_PWM_S00_AXI_SLV_REG0_OFFSET, 17179);//200hz                        |
|                                                                       |
| while (1) {                                                           |
|                                                                       |
| for (duty = 0x8fffffff; duty < 0xffffffff; duty = duty + 100000) {    |
|                                                                       |
| AX_PWM_mWriteReg(XPAR_AX_PWM_0_S00_AXI_BASEADDR,                      |
| AX_PWM_S00_AXI_SLV_REG1_OFFSET, duty);                                |
|                                                                       |
| usleep(100);                                                          |
|                                                                       |
| }                                                                     |
|                                                                       |
| }                                                                     |
|                                                                       |
| cleanup_platform();                                                   |
|                                                                       |
| **return** 0;                                                         |
|                                                                       |
| }                                                                     |
+-----------------------------------------------------------------------+

1) 通过运行代码，我们可以看到PL_LED1呈现出一个呼吸灯的效果。

.. image:: images/14_media/image34.png
   :width: 6.00069in
   :height: 4.09306in

7) 通过debug，我们来查看一下寄存器

.. image:: images/14_media/image35.png
   :width: 5.65486in
   :height: 3.81319in

8) 进入debug状态，按“F6”可以单步运行。

.. image:: images/14_media/image36.png
   :width: 5.70694in
   :height: 3.19306in

9) 通过菜单可以查看“Memory”窗口

.. image:: images/14_media/image37.png
   :width: 5.90903in
   :height: 3.28194in

10) 添加一个监视地址“0x43c00000”

.. image:: images/14_media/image38.png
   :width: 3.69563in
   :height: 2.11747in

.. image:: images/14_media/image39.png
   :width: 2.60273in
   :height: 1.87477in

11) 单步运行，观察变化

.. image:: images/14_media/image40.png
   :width: 6.00417in
   :height: 4.96199in

实验总结
--------

通过本实验我们掌握了更多的Vitis调试技巧，掌握了ARM +
FPGA开发的核心内容，就是ARM和FPGA数据交互。

常见问题
--------

如何知道AXI IP的基地址
~~~~~~~~~~~~~~~~~~~~~~

1) 如下图所示，打开“Address Editor”，可以看到地址分配情况

.. image:: images/14_media/image41.png
   :width: 5.90255in
   :height: 2.61484in

2) 地址一般是Vivado自动分配，我们也可以修改地址

.. image:: images/14_media/image42.png
   :width: 5.03918in
   :height: 1.63216in


*ZYNQ-7000开发平台 FPGA教程*    - `Alinx官方网站 <http://www.alinx.com>`_