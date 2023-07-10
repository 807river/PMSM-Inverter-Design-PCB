# PMSM-Inverter-Design-PCB 
<p>Documenting the progress of an attempt to design an inverter for a permanent magnet synchronous motor (three-phase).<br>
记录尝试设计永磁同步电机（三相）的逆变器的进展，希望能成功！（写的很乱，中英混杂，最后再整理吧）</p>
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
</ul>
<hr>

## 1背景知识

<p>本文提及的永磁同步电机为三相。Permanent Magnet Synchronous Motor (PMSM)，永磁同步电机。</p>
  
### 11永磁同步电机的基本结构

<p>永磁同步电机主要由定子和转子构成。定子包括三相绕组及铁心，电枢绕组常以Y型连接。转子结构有外转子结构，内转子结构。<br>
永磁同步电机的模式如Figure 1所示
<p align="center"><img src="https://github.com/807river/PMSM-Inverter-Design-PCB/assets/97770910/f0466dce-2d20-4954-bf52-aa474a5de13b" width="200px" height="200px" />
</p>
<p align="center"> Figure 1 永磁同步电机模型及坐标示意图</p>

### 12几种常见坐标系

<p>永磁同步电机的数学模型有</p>

### 13空间脉宽调制技术

### 14磁场定向控制

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
