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
      <li><a href="#12几种常见坐标系">12几种常见坐标系</a></li>
      <li><a href="#13空间脉宽调制技术">13空间脉宽调制技术</a></li>
      <li><a href="#14磁场定向控制">14磁场定向控制</a></li>
    </ul>
  <li><a href="#2使用的软件">2使用的软件</a></li>
    <ul>
      <li><a href="#21Altium-Designer">21Altium Designer</a></li>
      <li><a href="#22Code-Composer-Studio">22Code Composer Studio</a></li>
    </ul>
  <li><a href="#3逆变器DSP控制部分">3逆变器DSP控制部分</a></li>
    <ul>
      <li><a href="#31PWM信号驱动电路">31PWM信号驱动电路</a></li>
      <li><a href="#32检测电路">32检测电路</a></li>
      <li><a href="#33保护电路">33保护电路</a></li>
      <li><a href="#34电源">34电源</a></li>
    </ul>
  <li><a href="#4原理图绘制">4原理图绘制</a></li>
  <li><a href="#Reference">Reference</a></li>
</ul>
<hr>

## 1背景知识

<p>本文提及的永磁同步电机为三相。Permanent Magnet Synchronous Motor (PMSM)，永磁同步电机。</p>
  
### 11永磁同步电机的基本结构

<p>永磁同步电机主要由定子和转子构成。定子包括三相绕组及铁心，电枢绕组常以Y型连接。转子结构有外转子结构，内转子结构。<br>
  
永磁同步电机的模式如Figure 1[^1] 所示。
  
  <p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/214cb3f2-475f-4501-b92a-5a21bc897fa9" width="360px" height="208px" />
  </p>
  
<p align="center"> Figure 1 永磁同步电机模型及坐标示意图</p>

### 12几种常见坐标系

<p>永磁同步电机的数学模型有</p>

永磁同步电机的模型及坐标示意图如Figure 2所示。

  <p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/f0466dce-2d20-4954-bf52-aa474a5de13b" width="200px" height="200px" />
  </p>
<p align="center"> Figure 2 永磁同步电机模型及坐标示意图</p>

<p>为什么要针对永磁同步电机建立坐标系呢？是为了更精确的调速调频，为了研究精确的控制算法。<br>
  Figure 1中电机定子的三相绕组两两之间夹角为120°。</p>

 - ![abc坐标系](https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/c03476b0-fcbe-4690-b8a5-d9c0abb7e101)
  <p align="center"> Figure 3 A-B-C坐标系</p>

  - ![alpha-beta坐标系](https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/6c9e272f-cf1b-4428-a4f6-bfd5fee88292)
  <p align="center"> Figure 4 ɑ-β坐标系</p>
  
<p>如Figure 3和Figure 4所示， **A-B-C坐标系** 和 **ɑ-β坐标系** 都是依据定子的三相绕组建立的，它们与永磁同步电机相对静止。<br>
  
  - ![dq坐标系](https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/43932937-7d22-446f-beef-7bcdc6edf43d)
  <p align="center"> Figure 5 d-q坐标系</p>
  
<p>而Figure 5所示的 **d-q坐标系** 是两相旋转坐标系，是基于转子建立的。d轴沿转子的磁极轴线，d轴与A轴的所夹锐角定义为角度θ，所以 **d-q坐标系** 与永磁同步电机相对转动。
</p>

<p> 为了将交流电流和电压波形转换为直流信号，常连续采用Clarke变换和Park变换去实现[^2]。<br>
  
  - 将三相系统的时域分量（A-B-C坐标系）转换为正交静止坐标系（ɑ-β坐标系），称为Clarke变换。
  - 将正交静止坐标系的两个分量转换为一个正交旋转坐标系，称为Park变换。
</p>

### 13空间脉宽调制技术

### 14磁场定向控制 Field Oriented Control

<p>永磁同步电机的控制策略主要有：<br>
  
  - 恒压频比控制又称变压变频控制，Variable Voltage Variable Frequency (VVVF)；
  - 磁场定向矢量控制，Field Oriented Control (FOC)；
  - 直接转矩控制，Direct Torque Control (DTC)。</p>

<p>三种控制方法都是为了获得好的调速控制性能。<br>
  
  - VVVF的原理是利用 **”V/F=定值“** 的原理，用开环控制的方法控制电机[^3]。<br>
      根据电磁感应原理，气隙磁通在定子绕组每相绕组中的感应电动势E_m可以表示为：
      <h3 align="center" >E_m=4.44*F_s*N_s*K_ns*ɸ_m </h3>
      其中，F_s为定子频率，N_s为定子每相绕组串联匝数，K_ns为基波绕组系数，ɸ_m为气隙磁通有效值。<br>
      F_s,N_s,K_ns为常值，则E_m与Fs成正比。
      可以粗略理解为，VVVF的基本控制原则是控制气隙磁通有效值ɸ_m恒定不变。<br>
  - FOC是用坐标变换将三相交流电的控制，转换为 **产生转矩的q轴电流** 和 **产生磁场的d轴电流** 的控制，实现转矩和励磁的独立控制[^3]。
     从Figure 2可知，
  
  - DTC是

<hr>
  
## 2使用的软件
<p>The softwares used during design are Altium Designer, Code Composer Studio.</p>

### 21Altium Designer
    
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
    
### 22Code Composer Studio

<p>Code Composer Studio (CCS) is a software development tool for DSP. It could be got from https://www.ti.com/tool/CCSTUDIO.</p>

#### CCS的简单使用

<hr>

## 3逆变器DSP控制部分

### 31PWM信号驱动电路

### 32检测电路

### 33保护电路

### 34电源

<hr>

## 4原理图绘制

<hr>

## Reference:

[^1]: Zhao, Xiaokun, Baoquan Kou, Changchuang Huang, and Lu Zhang. 2022. "Optimization Design and Performance Analysis of a Reverse-Salient Permanent Magnet Synchronous Motor" Machines 10, no. 3: 204. https://doi.org/10.3390/machines10030204
[^2]: https://ww2.mathworks.cn/solutions/electrification/clarke-and-park-transforms.html
[^3]: https://blog.csdn.net/weixin_48005998/article/details/129597108?spm=1001.2101.3001.6650.8&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-8-129597108-blog-89455075.235%5Ev38%5Epc_relevant_anti_t3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-8-129597108-blog-89455075.235%5Ev38%5Epc_relevant_anti_t3&utm_relevant_index=12
