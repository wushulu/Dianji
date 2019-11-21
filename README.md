# Microchip 永磁同步点解控制方法

## 文件说明
   AN1078_code_use.docx 文档中有关应用程序中重要的变量，以及相关的计算 <br>
   evalua_baord.rar 文件为两种上位机调试软件配置 X2C_Scope 和 DMCI 
## 滑模参数的影响
### 滑模参数的两种计算方法
(1)客户和原厂给出的计算方法
![image](https://github.com/wushulu/Microchip-Motor/blob/master/%E5%9B%BE%E7%89%87/SMC_1.jpg)
(2)dome 代码中给出的计算方法<br>
``` c
	if (Q15(PHASERES * LOOPTIMEINSEC) > Q15(PHASEIND))
		s->Fsmopos = Q15(0.0);
	else
		s->Fsmopos = Q15(1 - PHASERES * LOOPTIMEINSEC / PHASEIND);

	if (Q15(LOOPTIMEINSEC) > Q15(PHASEIND))
		s->Gsmopos = Q15(0.99999);
	else
		s->Gsmopos = Q15(LOOPTIMEINSEC / PHASEIND * UMAX / IPEAK);

```

### beko电机调试
   beko 电机参数未知 滑模参数 G=1500 F=28000 切换速度为原来的2倍可以 顺利进入闭环<br>
   供电使用的是63V最大速度 Vmax=6800/Poles_pairs 极对数未知  
## 弱磁控制
   同智代码使用的弱磁控制，看上去为PID控制，但是前面判读条件有问题，代码在前面的判断条件上，使之一直运行一种条件。而且进入弱磁的控制条件是根据设置速度来判断的<br>
   demo程序的控制代码为，用的方法为查表法，要使用相关的电机参数。<br>
## desPIC 函数的使用
  __builtin 函数的使用 F1 搜索__builtin 可以得到具体的DSP函数 <br>
## 去你大爷的
   不要看一些网站的操作很容就把内容给清空了<br>
## 电机启动更快的方式
  同时给Q轴和D轴加电流 这样起来的速度回更快 成功率会更高<br>
  方式可以根据电机带负载特性来：<br>
  ```C
    CtrlParm.qVqRef = REFINAMPS(INITIALTORQUE);
    CtrlParm.qVdRef = REFINAMPS(INITIALTORQUE_D); // D轴电流值约Q轴的1/2
    //并且在进入闭环后 在D轴计算PID之前 判断角度值将D轴的电流清零
    if(Theta_error < 800) //800约8度
    {
      if(CtrlParm.qVdRef > 0)
        CtrlParm.qVdRef --;
    }
  ```

## 有感电机速度测量
  ```C
         if( Hall_r!=hall_value)
          {
            T2CONbits.TON = 0;
            Timer2Average = ((Timer2Average + Timer2Value + 2*TMR2)>>2);
    	    	Timer2Value = TMR2;
    	      TMR2 = 0;
            Hall_r=hall_value;
            T2CONbits.TON = 1;
            }
           Current_speed= TIMER2_TO_RPM/Timer2Average; // HZ/s   TIMER2_TO_RPM is timer2 frequency
           Crruent_RPM = Current_speed*20/16; //Crruent_RPM = Current_speed*60/6/8 (per RPM hall chang 6timers motor 8 pole pairs)
  ```
## 带编码器有感弦波控制
  (1)关于角度转化：将编码的值对2PI取模 ：机械角度（θ_m）与电气角度（θ_e）机械角度与电气角度的关系非常简单：θ_e=(θ_m⋅P)  % (2π) 其中，P是极对数，%是取模运算<br>
  (2) 有关速度测试：编码器15位 范围（0~32767）需要定义int16_t  做法是使用上一次的值 减去这一次的值得到稳定的差值 在对其进行使用buff 进行累加滤波。<br>
  (3)关于启动：首先观测取模后的角度和运行时的角度是不是存在相位差 一般相差90读或者是120度(具体值需要比较同一个速度和负载下的电机Q轴电流值 越小给的值约接近)。如果机械角度超前电气角度就减去相应的角度差。 在软件里面启动后直接进入闭环<br>
## 关于弱磁控制速度抖动问题
  (1)给的方法是让Id D轴电流和环路电流互不干扰。（有可能也含Q轴电流）<br>
## 提高FOC空载转速
   (1)弱磁控制 但是弱磁控制会增加电机的功耗一般电池的场合不开弱磁控制<br>
   (2)过调PWM占空比 这种方法提高PWM占空比达到和方波接近的控制，添加OverModulate()函数。<br>
## 电机功耗控制
   控制电机功耗主要是控制VS 限制VS的输出 可以根据Q轴的电流大小来确定调整VS的输出。<br>
   有关dspic 调高速度和降低功率的方案<br>
   待更新<br>
   git can not work<br>
   
   
