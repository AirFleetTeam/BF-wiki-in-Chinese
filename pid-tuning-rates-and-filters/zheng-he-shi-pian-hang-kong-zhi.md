# 整合式偏航控制

### 总览

整合偏航是一个用来解决四轴飞行器控制的基本问题的功能：Pitch/Roll由推力差来控制，而Yaw则不同。当电机的转速变化时，Yaw的变化主要是由于惯性引起的。在Yaw产生稳定的加速需要对角电机以稳定的速率增加/降低转速。

这就意味着在Yaw轴上，I的效果相当于P，而P的效果则相当于D。FF是一个控制元素，它会根据setpoint的移动速增加动力。  
  
这导致了Yaw轴上的几个问题。通常来说，Yaw最初会以一个非常快的速度对输入信号做出反应，然后随着时间的推移输出信号将回落，并再次开始追赶输入信号。

整合偏航功能通过在将Yaw的PID输入到混控器之前对其进行积分以解决这个问题。这将使PID的工作方式正常化。您现在可以使用调整其他轴的经验来调整Yaw。由于整合偏航功能不需要I，它将需要使用绝对控制功能。

### 设置

以下是启用它的CLI命令：

```text
set use_integrated_yaw=on
set iterm_rotation=off
set abs_control_gain=10
set p_yaw=30
set d_yaw=20
set i_yaw=0
set f_yaw=60
```

  
上述提供的PID仅供参考，您可能还需要再次调整它。请确保I是0或者是一个非常低的值，因为I将带来严重的震荡。同时可能会需要再次对Pitch和Roll的PIDF进行微调。

需要注意的是：

I再也没有了，或许它不再有意义了，但是在过去，CLI命令`set iterm_relax=RPY`是强制性的。
