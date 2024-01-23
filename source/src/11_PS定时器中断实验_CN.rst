PS定时器中断实验
=====================================

**实验VIvado工程为“ps_timer”。**

很多SOC内部都会有定时器，ZYNQ的PS也有，对于ZYNQ内到底有什么外设，这些外设有什么特性，都是开发者必须关心的，因此建议经常阅读xilinx文档UG585。

Vivado工程建立
--------------

1) 反复建立Vivado工程，其中有大量的重复性工作，最简单的解决方法就是把工程复制一份，再修改一下，打开工程“ps_hello”

2) 点击菜单“File ->Save Project As... ”

.. image:: images/11_media/image1.png
   :width: 2.85244in
   :height: 3.21199in

3) 在弹出的对话框中填写新的工程名“ps_timer”，选择创建工程子目录，PS里的定时器，因为不需要管脚输出，就不用配置管脚了

.. image:: images/11_media/image2.png
   :width: 4.3469in
   :height: 2.33228in

Vitis程序编写
-------------

创建Platform工程
~~~~~~~~~~~~~~~~

1) 点击ToolsLaunch Vitis

.. image:: images/11_media/image3.png
   :width: 3.47853in
   :height: 3.18532in

2) 与前面的Hello World实验不同，我们只建立Platform工程

.. image:: images/11_media/image4.png
   :alt: C:/Users/Administrator/Desktop/vivado_2023.1/AX7010_2023.1/7010_S2文档/images/images_2/image113.pngimage113
   :width: 5.98472in
   :height: 3.3875in

3) 填入工程名字，点击Next

.. image:: images/11_media/image5.png
   :alt: C:/Users/Administrator/Desktop/vivado_2023.1/AX7010_2023.1/7010_S2文档/images/images_2/image114.pngimage114
   :width: 5.10417in
   :height: 4.34722in

4) 点击“Create a new platform
   hardware(XSA)，软件已经提供了一些板卡的硬件平台，但对于

5) 我们自己的硬件平台，可以选择browse

.. image:: images/11_media/image6.png
   :alt: C:/Users/Administrator/Desktop/vivado_2023.1/AX7010_2023.1/7010_S2文档/images/images_2/image115.pngimage115
   :width: 4.79722in
   :height: 4.06042in

6) 选择XSA文件

.. image:: images/11_media/image7.png
   :alt: C:/Users/Administrator/Desktop/vivado_2023.1/AX7010_2023.1/7010_S2文档/images/images_2/image116.pngimage116
   :width: 6.9875in
   :height: 3.89514in

保持默认，点击Finish

.. image:: images/11_media/image8.png
   :width: 4.88165in
   :height: 3.93988in

7) 点开platform.spr，并点开BSP

.. image:: images/11_media/image9.png
   :width: 5.99514in
   :height: 2.96597in

8) 找到定时器驱动，并点击Import Examples

.. image:: images/11_media/image10.png
   :width: 5.99931in
   :height: 3.55278in

9) 非常幸运，有一个定时器中断的例子，怎么就知道这个例子就是中断的例子呢？是通过“intr”猜测的，所以，基本功很重要，不然你连找例程都不会。

.. image:: images/11_media/image11.png
   :width: 5.99722in
   :height: 3.84028in

10) 在这里就导入了example工程

.. image:: images/11_media/image12.png
   :width: 3.56626in
   :height: 3.04753in

下面就是阅读代码，然后修改代码了，当然，可能一下不能完全理解这些代码，只能在以后的应用中去反复练习

11) 本实验设计一个1秒定时器中断一次，然后打印出信息，30秒后结束，首先修改计数器最大值，修改为CPU频率的一半，也就是计数器的时钟频率值，这样就会1秒中断一次

.. image:: images/11_media/image13.png
   :width: 4.73275in
   :height: 2.75192in

12) 修改计数次数3改为30

.. image:: images/11_media/image14.png
   :width: 4.90871in
   :height: 1.93898in

13) 添加打印信息

.. image:: images/11_media/image15.png
   :width: 5.1039in
   :height: 2.97619in

1) 了解一下中断控制器的使用，主要分为几个步骤，初始化中断控制器\ *GIC初始化中断异常中断服务函数注册在中断控制器中使能中断使能外设中断使能中断异常*\ 。有两步需要注意，\ *在中断控制器中使能中断*
   是要根据中断号使能相应的中断，比如本章介绍的Timer为私有定时器，中断号为29，是在中断控制器GIC中的操作，而后面的\ *使能外设中断*
   是指在外设中打开它的中断，正常情况下是不打开的，打开之后就可以产生中断传递到中断控制器GIC。在以后的实验中可以借鉴这种写法。

.. image:: images/11_media/image16.png
   :width: 4.16667in
   :height: 6.42569in

.. image:: images/11_media/image17.png
   :width: 4.1349in
   :height: 0.65617in

下载调试
--------

1) 打开PuTTY串口终端

2) 下载调试程序的方法前面教程已经讲解，不再复述

.. image:: images/11_media/image18.png
   :width: 3.84097in
   :height: 3.08681in

3) 和我们预期一样，串口没秒会输出一句信息

.. image:: images/11_media/image19.png
   :width: 5.24778in
   :height: 3.29528in

实验总结
--------

实验中通过简单的修改Vitis的例程，就完成了定时器，中断的应用，看似简单的操作，可蕴含了丰富的知识，我们需要非常了解定时器的原理、中断的原理，这些基本知识是学习好ZYNQ的必要条件。
 

*ZYNQ-7000开发平台 FPGA教程*    - `Alinx官方网站 <http://www.alinx.com>`_