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
   demo程序的控制代码为，用的方法为查表法，要使用相关的电机参数。

   
   
