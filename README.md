# PMSM-Inverter-Design-PCB 
<p>Documenting the progress of an attempt to design an inverter for a permanent magnet synchronous motor (three-phase).<br>
记录尝试设计永磁同步电机（三相）的逆变器的进展，希望能成功！（写的很乱，中英混杂，最后再整理吧）<br>
走过路过，给个星星支持一下吧~</p>
<hr>

# Content目录
<ul>
  <li><a href="#1背景知识">1背景知识</a> </li>
    <ul>
      <li><a href="#11永磁同步电机的基本结构">11永磁同步电机的基本结构</a></li>
      <li><a href="#12逆变器的分类">12逆变器的分类</a></li>
      <li><a href="#13几种常见坐标系-ABC-ɑβ-dq">13几种常见坐标系 ABC ɑβ dq</a></li>
      <li><a href="#14几种常见的控制方法-VVVF-FOC-DTC">14几种常见的控制方法 VVVF FOC DTC</a></li>
      <li><a href="#15空间脉宽调制技术-PWM-SPWM-SVPWM">15空间脉宽调制技术 PWM SPWM SVPWM</a></li>
    </ul>
    <li><a href="#2逆变器DSP控制部分">2逆变器DSP控制部分</a></li>
    <ul>
      <li><a href="#21电源">21电源</a></li>
      <li><a href="#22PWM信号驱动电路">22PWM信号驱动电路</a></li>
      <li><a href="#23检测电路">23检测电路</a></li>
      <li><a href="#24保护电路">24保护电路</a></li>
    </ul>
    <li><a href="#3使用的软件">3使用的软件</a></li>
    <ul>
      <li><a href="#31Altium-Designer">31Altium Designer</a></li>
      <li><a href="#32Code-Composer-Studio">32Code Composer Studio</a></li>
    </ul>
    <li><a href="#4原理图绘制">4原理图绘制</a></li>
    <li><a href="#5额外的背景知识">5额外的背景知识</a></li>
    <li><a href="#Reference">Reference</a></li>
    </ul>
<hr>

## 1背景知识

<p>本文提及的永磁同步电机为三相。Permanent Magnet Synchronous Motor (PMSM)，永磁同步电机。</p>
  
### 11永磁同步电机的基本结构

<p>永磁同步电机主要由定子和转子构成。定子包括三相绕组及铁心，电枢绕组常以Y型连接。转子结构有外转子结构，内转子结构。<br>
  
永磁同步电机的模式如Figure 1[^1] 所示。
  
  <p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/8cacd882-9f5c-4f0d-94cc-4b3d18937321" width="360px" height="208px" />  </p>
<p align="center"> Figure 1 永磁同步电机模型及坐标示意图</p>

### 12逆变器的分类

在工业应用中，常常遇到需要将直流电转化为交流电的情况，逆变器Inverter便可以实现这样的功能。比如在电动汽车中，动力电池输出的电是直流电，驱动汽车运动的电机所需要的是三相交流电。<br>
逆变器的分类表1所示如下[^2]：

<p align="center"> Table 1 逆变器的分类
  
  | 分类标准 | 三相逆变器的细分 | 三相逆变器的细分 |
  | :-: | :-: | :-: |
  | 直流电源性质 | 电压源电路 | 电流源电路 |
  | 控制方式 | 方波电路 | PWM电路 |
  | 输出相电压的电平数 | 两电平电路 | 多电平电路 |
  | 电路结构 | 半桥电路 | 全桥电路 |
  | 负载性质 | 无源负载 | 有源负载 |
  
  </p>
<p></p>有源逆变器，其逆变器的输出需要直接并入电网。因为输出端有电压波动的影响，逆变器知识向外输出能量，逆变器可以自动关断，所以无需在控制时施加强制关断，因此可以使用廉价的半控型器件，如晶闸管。<br>

无源逆变器，其逆变器的输出需要直接使用，不需要并入电网。考虑到输出端没有电压波动，逆变器需要严格控制关断，所以必须使用全控型器件，如可关断晶闸管等[^3]。</p>

单相桥式逆变电路的电阻负载简化电路如下所示。
 <p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/310a8e6c-6858-4820-bb41-7a0f8963d9dd" width="360px" height="208px" />
 <p align="center"> Figure 2 单相桥式逆变电路的电阻负载简化电路</p>

逆变电路Inverter Circuit是可以把直流电变换为交流电的电路。根据逆变电路的电路形式与输出的交流信号，可将逆变电路分为全桥逆变电路，半桥逆变电路和三相桥式逆变电路。

  - 全桥逆变电路
    由各含两个开关的两个桥臂连接成正方形，输出端的两端分别位于两组开关的中点，相当于取两个半桥的电压差，因此可以得到正负双向的交流输出。全桥逆变器可以不依赖外加器件，仅仅使用单电压源输出双端的完全交流/含有直流分量的交流以及完全直流信号。
  - 半桥逆变电路
    由两个开关串联组成，输出端位于两个开关的中点，由上下两个开关的开通/关断来决定输出的电压。半桥逆变电路配合两个分压电容，可以输出双端之间的高频交流电。开关旁一般需要并联续流二极管，以便在感性负载时起到续流作用。半桥逆变器配合正负双电压源，可以输出双端的完全交流/含有直流分量的交流以及完全直流信号。
  - 三相桥式逆变电路
    三相桥式逆变电路类似于全桥逆变电路，但其中有三个桥臂，输出端的三端分别位于三组开关的中点，取两两之间的电压差就可以得到三相电所需的三个相电压。根据三组共六个开关的开通顺序，三相桥式逆变器可以得到一组幅值相等/频率相等/相位相差120°的三相电信号。[^3]
<hr>

### 13几种常见坐标系 ABC ɑβ dq

永磁同步电机的模型及坐标示意图如Figure 2所示。

  <p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/f0466dce-2d20-4954-bf52-aa474a5de13b" width="200px" height="200px" />
  </p>
<p align="center"> Figure 3 永磁同步电机模型及坐标示意图</p>

<p>为什么要针对永磁同步电机建立坐标系呢？是为了更精确的调速调频，为了研究精确的控制算法。<br>
  Figure 1中电机定子的三相绕组两两之间夹角为120°。</p>

<p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/c03476b0-fcbe-4690-b8a5-d9c0abb7e101” width="200px" height="200px" />
<p align="center"> Figure 4 A-B-C坐标系</p>

<p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/6c9e272f-cf1b-4428-a4f6-bfd5fee88292” width="200px" height="200px" />
<p align="center"> Figure 5 ɑ-β坐标系</p>
  
如Figure 3和Figure 4所示，**A-B-C坐标系**和**ɑ-β坐标系**都是依据定子的三相绕组建立的，它们与永磁同步电机相对静止。

<p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/43932937-7d22-446f-beef-7bcdc6edf43d” width="200px" height="200px" />
<p align="center"> Figure 6 d-q坐标系</p>
  
而Figure 5所示的**d-q坐标系**是两相旋转坐标系，是基于转子建立的。d轴沿转子的磁极轴线，d轴与A轴的所夹锐角定义为角度θ，所以**d-q坐标系**与永磁同步电机相对转动。

为了将交流电流和电压波形转换为直流信号，常连续采用Clarke变换和Park变换去实现[^4]。<br>
  
  - 将三相系统的时域分量（A-B-C坐标系）转换为正交静止坐标系（ɑ-β坐标系），称为Clarke变换。
  - 将正交静止坐标系的两个分量转换为一个正交旋转坐标系，称为Park变换。

### 14几种常见的控制方法 VVVF FOC DTC

对永磁同步电机的控制都是为了获得好的调速控制性能。常见的控制方法有三种，它们分别是有：<br>
  
  - **恒压频比控制又称变压变频控制，Variable Voltage Variable Frequency (VVVF)**<br>
    VVVF的原理是利用**”V/F=定值“**的原理，用开环控制的方法控制电机[^5]。<br>
      根据电磁感应原理，气隙磁通在定子绕组每相绕组中的感应电动势E_m可以表示为：
      <h3 align="center" >E_m=4.44*F_s*N_s*K_ns*ɸ_m </h3>
      其中，F_s为定子频率，N_s为定子每相绕组串联匝数，K_ns为基波绕组系数，ɸ_m为气隙磁通有效值。<br>
      F_s,N_s,K_ns为常值，则E_m与Fs成正比。
      可以粗略理解为，VVVF的基本控制原则是控制气隙磁通有效值ɸ_m恒定不变。<br>
      
  - **磁场定向矢量控制，Field Oriented Control (FOC)**<br>
    FOC是用坐标变换将三相交流电的控制，转换为 **产生转矩的q轴电流** 和 **产生磁场的d轴电流** 的控制，实现转矩和励磁的独立控制。
     从Figure 3可知，
    
  - **直接转矩控制，Direct Torque Control (DTC)**<br>
    DTC是通过直接控制定子磁链来直接控制电机的电磁转矩。这种方法不需要转子位置信息和电机的转子参数，省去了复杂的旋转坐标变换。对于永磁同步电机来说，使用这种方法必须已知转子的初始位置。

### 15空间脉宽调制技术 PWM SPWM SVPWM

  - **脉冲宽度调制，Pulse Width Modulation (PWM)**<br>
  PWM可以将模拟信号变换为脉冲，且变换后的脉冲周期可以根据模拟信号大小的改变而改变。PWM技术实现的基本原理是面积等效原理，即冲量相等而形状不同的窄脉冲加在具有惯性的环节上时，其效果基本相同。<br>
    
  - **正弦脉宽调制，Sinusoidal Pulse Width Modulation (SPWM)**<br>
正弦脉宽调制法是将每一个正弦周期内的多个脉冲作规律的脉宽调制。SPWM也是基于PWM实现的，输入一段幅值相等的脉冲序列去等效正弦波，输出的脉冲时间宽度基本符合正弦变化。常用的采样方法有：自然采样法和规则采样法[^6]。<br>
    
  - **空间脉宽调制法，Space Vector Pulse Width Modulation (SVPWM)**<br>
这种技术是基于平均值等效原理，即在一个开关周期内通过对基本电压矢量加以组合，使其平均值与给定电压适量相等。可以理解为，在某个时刻，电压矢量旋转到某个区域中，可以由组成这个区域的两个相邻的非零矢量和零矢量在时间上的不同组合来得到。两个矢量的作用时间在一个采样周期内分多次施加，可以控制各个电压矢量的作用时间，使电压空间矢量接近圆轨迹旋转。通过逆变器的不同开关状态所产生的实际磁通去逼近理想磁通圆，并由两者的比较结果来决定逆变器的开关状态，从而形成PWM波形[^7]。<br>
    在本文的实际应用就是控制三相逆变桥的六个IGBT元件瞬时开关的状态，产生特定的脉宽调制波，可以使电机获得理想圆形磁链轨迹，输出理想的正弦电流波形。

## 2逆变器DSP控制部分

永磁同步电机矢量控制系统基于DSP的实现，拟采用TI公司的DSP芯片。其中F283xx系列DSP是专门设计的电机控制类处理器，配有浮点处理单元，具有计算能力强/外设功能强大等优点。<br>
对三相交流同步电机矢量控制系统的设计，需要用到ADC采样模块/ePWM等模块。主电路一般采用典型的交-直-交电压源型变频器结构，先将三相交流电整流，再通过电压源型逆变器给电机供电。基于这几个设计条件：<br>

  - 主电路使用两电平电压源型逆变器，共需6路控制脉冲；
  - 三相永磁同步电机使用Y型接法，且不带中线；
  - 使用TMS320F283xx DSP作为主控制器；
  - 功率器件的开关频率为5kHz，即中断周期为200μs。

设计IGBT驱动电路的时候，需要考虑到信号发生电路，信号隔离电路和驱动电路的设计[^9]。<br>
1)**信号发生电路**<br>
IGBT一般需要15V的驱动电压，但是控制器能够提供的电压是3.3-5V，所以需要设计信号发生电路使电压满足设计需要。并且信号发生电路要**产生两路带死区互补的方波信号**，否则会造成IGBT的误导通。<br>
2)**信号隔离电路**<br>
由于逆变电路中的高压极易通过驱动电路对控制电路的低压形成干扰，所以需要对驱动信号和IGBT的连接进行电气上的隔离。常用的方法有变压器隔离和光耦隔离。<br>
3)**驱动电路**<br>
由于信号发生电路产生的控制信号电流小，幅值低，无法进行IGBT的快速导通和关断，因此需要MOS驱动器进行放大，增加其功率，使其能够快速导通和关断MOS管。<br>

设计逆变桥的时候，有全桥逆变，单管逆变，和半桥逆变。全桥逆变电路的输出功率更高，开关损耗更小，可接纳的控制方式更多。<br>
**设计全桥电路的三个要点**：<br>
1）MOS/IGBT的栅极引脚部分的电路；<br>
2）MOS/IGBT的保护电路；<br>
MOS/IGBT具有较脆弱的承受短时过载能力，所以在应用的时候需要为其设计合理的保护电路来提高器件的可靠性。常见的保护电路有很多种，比如RC保护电路，或者RCD保护电路。？？这里放几张图就更好了<br>
3）全桥电路的器件选型问题。<br>
全桥电路中每一个器件的选型都需要考虑它的承受值。比如MOS管的选型，在计算最大工作频率是要关注MOS管的上升和下降时间，以保护电路的器件特性。<br>

### 21电源

设计供电电源电路的时候，需要考虑到隔离。供电电路为控制系统提供+15V，-15V和+5V的直流电压。设计时供电电路采用两个变压器，分别对控制保护部分和功率模块部分提供电源，其中给功率模块供电变压器的直流输出要分别绕线，不能有公共结点[^10]。

### 22PWM信号驱动电路


### 23检测电路

### 24保护电路

<hr>

## 3使用的软件
<p>The softwares used during design are Altium Designer, Code Composer Studio.</p>

### 31Altium Designer
    
<p>Altium Designer (AD) is very helpful for drawing circuit diagrams. It could be got from https://www.altium.com/altium-designer.</p>

#### AD的简单使用

  - 原理图库的创建
  - 常见原理图库的绘制
  - 元件的复制，剪切，旋转及镜像
  - 导线的绘制及导线的属性
  - 非电气对象（辅助线，文字）的放置
  - 元器件的位号编号
  - 检查原理图库的正确性
  - BOM物料表的导出
    
### 32Code Composer Studio

<p>Code Composer Studio (CCS) is a software development tool for DSP. It could be got from https://www.ti.com/tool/CCSTUDIO.</p>

#### CCS的简单使用

  - 配置开发环境
  - 外部中断
  - ePWM
  - ADC转换
  - DAC转换
  - GPIO
<hr>

## 4原理图绘制

## 5额外的背景知识


<hr>

## Reference:

[^1]: Zhao, Xiaokun, Baoquan Kou, Changchuang Huang, and Lu Zhang. 2022. "Optimization Design and Performance Analysis of a Reverse-Salient Permanent Magnet Synchronous Motor" Machines 10, no. 3: 204. https://doi.org/10.3390/machines10030204

[^2]: https://zhuanlan.zhihu.com/p/256406416

[^3]: https://zh.wikipedia.org/zh-hans/%E9%80%86%E8%AE%8A%E5%99%A8

[^4]: https://ww2.mathworks.cn/solutions/electrification/clarke-and-park-transforms.html

[^5]: https://blog.csdn.net/weixin_48005998/article/details/129597108?spm=1001.2101.3001.6650.8&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-8-129597108-blog-89455075.235%5Ev38%5Epc_relevant_anti_t3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-8-129597108-blog-89455075.235%5Ev38%5Epc_relevant_anti_t3&utm_relevant_index=12

[^6]: https://blog.csdn.net/u010632165/article/details/110889621

[^7]: https://blog.csdn.net/qlexcel/article/details/74787619

[^9]: https://blog.csdn.net/weixin_44586889/article/details/109734817

[^10]: 殷健翔.(2021).基于TMS320F28379D的多电机同步控制策略研究(硕士学位论文,浙江大学).https://kns.cnki.net/kcms2/article/abstract?v=EeZTdI0aL7sEApdvxu7_eJdzvF5UeACxBHVVHwQYb-v2mjYsV5uY0zIVw6BjoFiLQNbu_StuNgwG95GelBflPLa_1nkvRc6mnWqlzehzIfygl22AAjFmP7GPZJmYHFuY69X5igd8qbg=&uniplatform=NZKPT&language=CHS


