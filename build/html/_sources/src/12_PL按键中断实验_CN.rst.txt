PL按键中断实验
===================================

**实验VIvado工程为“ps_axi_key”。**

前面的定时器中断实验的中断属于PS内部的中断，本实验中断来自PL，PS最大可以接收16个来自PL的中断信号，都是上升沿或高电平触发。本实验用按键中断来控制LED。

.. image:: images/12_media/image1.png
   :width: 5.41444in
   :height: 3.40355in

Vivado工程建立
--------------

1) 本实验所用的Vivado工程只需要在“ps_axi_led”这个工程上添加用于按键输入的AXI
      GPIO就可以，点击菜单“File -> Save Project As...”

.. image:: images/12_media/image2.png
   :width: 3.75332in
   :height: 2.28255in

2) 新的工程名为“ps_axi_key”

.. image:: images/12_media/image3.png
   :width: 4.11357in
   :height: 2.20709in

3) 添加一个AXI GPIO

.. image:: images/12_media/image4.png
   :width: 3.11185in
   :height: 3.1715in

4) 配置GPIO参数，都为输入，宽度为1，使能中断

.. image:: images/12_media/image5.png
   :width: 6.00417in
   :height: 4.60569in

5) 使用自动连接

.. image:: images/12_media/image6.png
   :width: 4.39949in
   :height: 2.8636in

6) 再把端口名称改为keys

.. image:: images/12_media/image7.png
   :width: 4.29793in
   :height: 2.38649in

7) 配置ZYNQ处理器的中断，勾选IRQ_F2P

.. image:: images/12_media/image8.png
   :width: 6.00417in
   :height: 4.60569in

8) 连接ip2intc_irpt到IRQ_F2Q

.. image:: images/12_media/image9.png
   :width: 6.00417in
   :height: 2.39822in

9) 修改xdc约束文件

+-----------------------------------------------------------------------+
| set_property IOSTANDARD LVCMOS15 [get_ports {leds_tri_o[0]}]          |
|                                                                       |
| set_property IOSTANDARD LVCMOS15 [get_ports {leds_tri_o[1]}]          |
|                                                                       |
| set_property IOSTANDARD LVCMOS15 [get_ports {leds_tri_o[2]}]          |
|                                                                       |
| set_property IOSTANDARD LVCMOS15 [get_ports {leds_tri_o[3]}]          |
|                                                                       |
| set_property PACKAGE_PIN F5 [get_ports {leds_tri_o[0]}]               |
|                                                                       |
| set_property PACKAGE_PIN E5 [get_ports {leds_tri_o[1]}]               |
|                                                                       |
| set_property PACKAGE_PIN G5 [get_ports {leds_tri_o[2]}]               |
|                                                                       |
| set_property PACKAGE_PIN G6 [get_ports {leds_tri_o[3]}]               |
|                                                                       |
| set_property IOSTANDARD LVCMOS15 [get_ports {keys_tri_i[0]}]          |
|                                                                       |
| set_property PACKAGE_PIN H6 [get_ports {keys_tri_i[0]}]               |
+-----------------------------------------------------------------------+

1)  保存设计，编译生成bit文件

下载调试
--------

1) 点击FileLaunch Vitis进入Vitis

..

   .. image:: images/12_media/image10.png
      :width: 2.81875in
      :height: 2.58056in

2) 新建platform工程过程不再赘述，参考“PS点亮PL的LED灯” 一章

.. image:: images/12_media/image11.png
   :width: 5.99514in
   :height: 2.96597in

3) 和前面的教程一样，在不熟悉Vitis程序编写的情况下，我们尽量使用Vitis自带例程来修改

.. image:: images/12_media/image12.png
   :width: 6.00278in
   :height: 2.86597in

4) 选择“xgpio_intr_tapp_example”

.. image:: images/12_media/image13.png
   :width: 5.99722in
   :height: 3.86528in

5) 导入例程以后有未定义的错误，我们需要修改部分代码

.. image:: images/12_media/image14.png
   :width: 5.99306in
   :height: 3.45278in

6) 按下图修改GPIO和中断号的宏定义

.. image:: images/12_media/image15.png
   :width: 4.76266in
   :height: 3.62812in

7) 修改测试延时时间，让我们有足够的时间去按按键

.. image:: images/12_media/image16.png
   :width: 5.55207in
   :height: 4.39663in

8) 打开串口终端，运行程序

.. image:: images/12_media/image17.png
   :width: 4.93056in
   :height: 3.36319in

9) 如果一直不按按键，串口显示“No button
   pressed.”，如果按下“PL_KEY”显示“Successfully ran Gpio Interrupt Tapp
   Example”。

.. image:: images/12_media/image18.png
   :width: 5.43905in
   :height: 3.41538in

实验总结
--------

PL端可以给PS发送中断信号，这提高了PL和PS数据交互的效率，在需要大数量、低延时的应用中需要用到中断处理。


*ZYNQ-7000开发平台 FPGA教程*    - `Alinx官方网站 <http://www.alinx.com>`_