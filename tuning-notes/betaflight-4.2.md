# Betaflight 4.2

### **Betaflight 4.2 带来了…**

* **更准确的循环时间** - 改善了RPM滤波器性能
* **改进了的前馈** - 使飞行更加顺畅，下文附有摘要，用以优化平滑设置，以达到电影式飞行的目的
* **新的动态Dterm滤波截止频率曲线** - 通过提高Dterm滤波器截止频率的移动速度来加强过冲抑制能力
* **电池压降动态补偿** - 为飞行提供一致的油门响应和PID“感觉”
* **新的Rate模式** - 更直观地调节那些重要参数，并为竞速和电影飞行用途提供更线性的“操控感”
* **改进的动态陷波滤波器** - 更好的噪声抑制能力，简化的最大/最小截止频率配置
* **自动抗自旋配置** - 更快地抑止偏航自旋
* **独立于I值的反重力增益** - 若更改I，反重力的效果强度不会随之改变
* **更简单、更好用的RC平滑** - 仔细优化过的默认配置意味着几乎不再需要手动设置平滑参数
* **I值释放可在设定点模式下正常工作** - 修复了4.0和4.1中的一个错误。该错误导致在设定点模式下降低溢出值的作用很小。现在设定点模式可正常运行，由此错误而被迫使用陀螺仪模式的用户，应返回到设定点模式。
* **NFE竞速模式** - 一种非常有趣的飞行模式，Pitch手动，Roll自稳。
* **配置程序10.7** - 进行了许多改进，尤其是可以正确显示不同Rate模式下对应的实际曲线
* **OSD改进** - 现在每次解锁时都可短暂显示启动徽标；CRSF会同时显示当前模式和信号强度；添加了相机取景框，距离返航点距离，电池效率等新元素。

  
**对于绝大多数飞机来说，4.2的默认值可以提供相当优秀的飞行体验，无需进行修改。**如果您非常清楚您的飞机确实需要使用特殊的PID，请务必重新调参，因为4.2中的新功能，会使得同样的PID产生不同的结果。例如，如果您希望D值非常高而不受噪声强度限制，旧版本中不行，现在却可以。  
  
**某些弱动力飞机（弱动力小无刷、涵道式摄像飞机、7寸及更大的飞机、长续航飞机等）可能需要一些特殊设置以减小回弹。**  
如果您不熟悉4.2，请先阅读[《4.1调参指南》](betaflight-4.1.md)和[《4.0调参指南》](betaflight-4.0.md)。  
**请使用最新版本的Betaflight配置程序，早期版本将无法与4.2搭配使用。**

**注意1：**请勿将任何先前版本的diff或dump输出直接粘贴到4.2的CLI中。

**注意2：**如果您在先前版本将dyn\_notch\_range设置为LOW或HIGH，请将dyn\_notch\_max\_hz动态陷波滤波最大频率设置为350或700.

**注意3：**如果您以前的PID的I值一直保持默认值，那么在刷写4.2之后您需要对反重力增益稍作修改才能获得相同的效果。

**注意4：**请先从默认滤波和PID开始调整，除非您十分确信您的飞机需要进行特殊设置。

**注意5：**我们建议启用RPM滤波器。[点击这里](http://mp.weixin.qq.com/s?__biz=Mzg4OTAzNzM5MA==&mid=2247484384&idx=1&sn=0b0debb82c7e3b6ad0de227a830ad7b9&chksm=cff0b4c1f8873dd72e267f22644bc23fa7a388f19b277dce35acc8cf17461c4b76feb3c3853c&scene=21#wechat_redirect)阅读有关启用和配置PRM滤波器的详细信息。

### **快速设置**

  
下列是一些参考性的配置片段，包含了一些并不是很常见的设置，以适应不同的飞行风格。对于普通的花飞和竞速来说，默认值应该比较合适。我们无法保证它将完美适配您的飞机，而且他们不包括PID参数或滤波设置，但它可以为其中某些数字提供一定的参数范围参考。

**专业竞速**\(激进式前馈，需要有非常干净的RC信号，否则将可能因为RC质量不良引起的RC数据帧抖动而导致电机过热\)

```text
set iterm_relax_cutoff = 30
set rc_smoothing_auto_smoothness = 5
set ff_interpolate_sp = ON
set ff_smooth_factor = 0
set ff_spike_limit = 60set ff_boost = 20set feedforward_transition = 0
set yaw_lowpass_hz = 100
set throttle_boost = 7
set throttle_boost_cutoff = 25
set dyn_lpf_dterm_curve_expo = 7
set gyro_rpm_notch_q = 600
```

（启用电池电压补偿，只要适用于您的飞机，那么值越高越好，D expo也是如此）

**普通花飞/竞速**\(强前馈，可应对常见的RC信号丢帧情况，反应灵敏\)

```text
set iterm_relax_cutoff = 20
set rc_smoothing_auto_smoothness = 7
set ff_interpolate_sp = AVERAGED_2
set ff_smooth_factor = 20
set ff_spike_limit = 70
set ff_boost = 15
set feedforward_transition = 0
set yaw_lowpass_hz = 100
set throttle_boost = 7
set throttle_boost_cutoff = 25
set dyn_lpf_dterm_curve_expo = 7
set gyro_rpm_notch_q = 700
```

**HD**\(用于拍摄高清视频的平滑前馈，低速转弯十分平稳，较低的I值释放阈值以最小化回弹\)

```text
set iterm_relax_cutoff = 10
set rc_smoothing_auto_smoothness = 20
set ff_interpolate_sp = AVERAGED_3
set ff_smooth_factor = 40
set ff_spike_limit = 55
set ff_boost = 0
set feedforward_transition = 40
set yaw_lowpass_hz = 70
set throttle_boost = 5
set throttle_boost_cutoff = 10
set dyn_lpf_dterm_curve_expo = 7
set gyro_rpm_notch_q = 800
```

**电影式飞行**\(仅限低速转弯，否则飞起来会觉得很“肉”\)

```text
set iterm_relax_cutoff = 5
set rc_smoothing_auto_smoothness = 40
set ff_interpolate_sp = AVERAGED_4
set ff_smooth_factor = 50
set ff_spike_limit = 50
set ff_boost = 0
set feedforward_transition = 70
set yaw_lowpass_hz = 50
set throttle_boost = 2
set throttle_boost_cutoff = 10
set dyn_lpf_dterm_curve_expo = 8
set gyro_rpm_notch_q = 900
set iterm_windup = 75
```

下面的调参建议可能有助于最大程度地减小高清视频中的轻微随机抖动：

* 禁用D\_Min
* 使用PD比滑块**，**将D设置得比P高约20%
* 将TPA设置为仅衰减D，从您的巡航油门值起始，并稍微增加衰减百分比
* 将Dterm低通滤波expo设置的尽可能高（您会受到中油门附近的D噪声限制）（当D更高，您可能需要更多的Dterm滤波，例如将Dterm滤波滑块向左移动两个格子）
* P值比普通的花式飞行P值低20%，使得P仅仅能够提供基本的稳定性
* I值降低一半
* 提高FF过滤强度
* 将Rate类型设为actual，中央灵敏度设置为10，Expo设为0，最大Rate使用您常用的最大Rate。当Expo为0可以为中央杆位提供柔和的感觉，可以使您在摇杆偏离中央时快速反应过来。当中央灵敏度非常低时，可能不需要前馈过渡/死区。

**返回默认值**\(值为0意味着“关闭”\)

```text
set iterm_relax_cutoff = 15
set rc_smoothing_auto_smoothness = 10
set ff_interpolate_sp = AVERAGED_2
set ff_smooth_factor = 37	
set ff_spike_limit = 60
set ff_boost = 15
set feedforward_transition = 0
set yaw_lowpass_hz = 0
set throttle_boost = 5
set throttle_boost_cutoff = 15
set dyn_lpf_dterm_curve_expo = 5
set gyro_rpm_notch_q = 500
set iterm_windup = 100
```

**一次性开启其他所有推荐使用的新功能**

```text
set dyn_lpf_dterm_curve_expo = 6
set vbat_sag_compensation = 100
set vbat_pid_gain = OFF
set rc_smoothing_type = FILTER
set rc_smoothing_input_hz = 0
set rc_smoothing_derivative_hz = 0
set rc_smoothing_input_type = BIQUAD
set rc_smoothing_derivative_type = PT1
set rc_smoothing_auto_smoothness = 10
```

开启电池压降动态补偿

```text
set vbat_sag_compensation = 100
set vbat_pid_gain = OFF
```

### **最小化回弹的设置**

目前，Betaflight默认值旨在给予中等性能的四轴飞行器最出色的表现，“中等性能”指的是大多数重量较轻，响应性良好的5寸飞机和更小的飞机。

“弱动力”飞机，例如弱动力小无刷、涵道HD摄像飞机、重型4s花飞飞机和许多7寸及更大的飞机，它们所具有的共同点是，相较于“中等性能”的飞机来说，在快速翻滚以及快速进行偏航转弯时，它们往往都会发生回弹。

本段解释了这些飞机会回弹的原因，并将给出解决回弹的方法。

在Betaflight的PID中，I用于提供转弯时的精度、快速平飞时的俯仰精度、大风情况下的稳定性、自由落体和半圈翻滚时的稳定性，控制不对称的空气阻力效应，并在通常情况下清理掉无数无法被P处理掉的持久性残差。随着时间的推移，它会积累一些小的残差的校正值，而该校正值可以使飞机保持正确的姿态。较高的I值将更快、更完整的执行校正值，尤其是在转弯时和大风天，在这种情况下，无法被P、D或FF消除的残差将很小。相对较高的I是Betaflight 4.x在急转弯和长直道上飞行如此精准的原因之一。

如果飞行员打杆速率过快而飞机无法及时响应，则陀螺仪信号将落后于设定点，并且将产生较大的误差信号。这时I将开始积累以修正此问题。I值释放将尝试控制这种积累。若I值被释放得很少，不足以弥补积累速度，则可能会导致Iterm大量积累，并且当飞行员杆位回中时，所有积累下的I都将会将飞机向反方向驱使动作，该动作会逐渐消失，直到角速度归零。这就是飞行员在翻滚结束时看到的那个缓慢的“回弹”动作。

这种动作称为“积分饱和”（Iterm windup）。

我们使用I值释放（iterm\_relax）和I值饱和（iterm\_windup）两个功能来控制与I值有关的回弹。在4.2中这些功能的默认设置对于“中等性能”飞机非常有效。I值释放默认值为15，在正常的翻滚期间会强烈抑制I的积累。I值饱和则适用于偏航问题，并且由于通常情况下根本不需要他，故默认情况下I值饱和关闭。

但是，“弱动力”飞机在快速翻滚及快速掉头后往往会回弹，这是因为从输入信号侧来观察，它们比正常情况具有更高的延迟和更大的误差。在这种情况下，需要为这些机器调整I值释放和I值饱和参数。

当您发现在进行600°/s的翻滚/快速掉头**，**和/或快速打Yaw杆之后出现了烦人的回弹，您就应当意识到您的飞机动力较弱。弱动力飞机的其他表征线索是，您的悬停油门比正常值高（例如您的悬停油门高于25%），或者下落或低油门滑行时产生了缓慢的晃动。

对于偏航来说，任何飞机机臂过长 - 大于6英寸 - 都将导致偏航动力偏弱，并且通常会出现偏航回弹和偏航跳跃问题。

可以通过以下三种方式解决由于I值饱和引起的回弹问题。

**1.最好的方法是调整参数或更改飞机的硬件，以使其响应速度变快。**理想情况下，PID参数匹配的飞机可以在其余功能保持默认的情况下响应得很快。这也将改善飞机的整体处理能力。让我们考虑一下如何做到这一点。

弱动力飞机通常需要较高的P和FF。在某些情况下，可能需要两倍的P和三倍的FF。所有PIDF参数增益都有一个上限，但是您需要尽可能提高。D很可能需要和P同时增大，否则将会导致Pterm震荡，而Dterm噪声则限制了您究竟可以将D增大到多少。您应该向右大幅推动“**摇杆响应增益**”滑块，并且将“**P和D增益**”滑块逐步向右滑动。这将能使机器更快地旋转并减小误差幅值和持续时间，所以它们应当可以减少晃动和回弹。请注意，D过高会延长响应速度，并加重回弹现象。所以在这些机器上使用正确的PD值确实很重要。

假如调整了P和FF，但仍然有晃动和回弹，则应尝试降低I，将其值设为默认值的一半或三分之一。若I过低则将使得飞机在长周期内不稳定，所以也不要将其降得过低。

在硬件方面，弱电机-重桨这样的配置将使得飞机很难快速地改变推力。应该尽量使用较轻较小的螺旋桨，以使得电机更容易旋转。使用与电机动力匹配的螺旋桨可以大大改善因弱动力造成的问题，例如回弹。

在大型飞机上，例如beat-class或xclass，使用螺旋桨内旋（prop-in）布局会提高偏航性能。

任何降低回弹的更改都意味着，您可以令飞机更快速准确地对输入信号做出反应，这是衡量调参成功与否的重要指标。

**2.第二个方法是降低俯仰、横滚或偏航的Rate，或者只是更平稳地飞行**，而使快速翻滚的开始与结束动作稍慢一些。如果不再恶狠狠地压榨硬件的极限，您将会得到良好的整体性能表现，而无需参数优化到极致。

**3.调整我们提供的I值释放（iterm\_relax）和I值饱和（iterm\_windup）功能**

Betaflight使用iterm\_relax来防止快速的翻滚指令产生不必要的I值积累。

其参数iterm\_relax\_cutoff用于设定抑制效果的强度及其持续时间。较低的值会在更长的时间内以更高的强度来抑制I值，这是抑制I值造成的回弹的必要条件。

首先应尝试将iterm\_relax\_cutoff从默认值15降低到10，然后是7，然后是5，注意每次调整后回弹情况都会有所改善。在其“有效工作”的前提下选择尽可能高的值。截止频率越低，转弯就越不准确，因此您需要衡量取舍。您应当选择刚好可以控制回弹现象的最高值。

我们强烈建议使用默认的设定点模式下的I值释放。在4.2中，您无需将其更改为陀螺仪模式，设定点模式下的I值释放可能比4.2中的陀螺仪模式效果更好。

对于偏航存在的回弹问题，首先应当尝试调整偏航PID，优化偏航PID可以改善回弹问题。当您进行偏航动作时，FF会在移动摇杆时提供初始的“推动力”，P会在不久之后提供“推动力”，而I将完成大部分偏航动作。为了获得良好的偏航性能，需要同时使用到这三个元素。

理想情况下，您需要录制日志，用以作为调整P、I和FF的参考依据。应尽量将偏航轴的中等角速率以下的响应延迟，调整至与俯仰和横滚的响应延迟大致相同。

在黑盒日志中您将会看到，对于较高的偏航角速度指令，电机会很快出现较大的输出命令差，其中一对同转向电机油门将达到100%，另一对电机油门为0。这意味着飞机无法执行您所期望的偏航角速度。当发生这种情况时，积分饱和和回弹是不可避免的。

I值饱和（iterm\_windup）解决了偏航轴的这一问题。在旧版本中它作用于所有轴，但在4.2中它专门作用于偏航轴。当电机输出命令差值超过预设的百分比时，它将抑制I值的积累。默认值为100，表示该功能默认关闭。将其设为70可以很好地防止飞机发生偏航回弹。

当电机输出命令差值超过70%时，通过对偏航积分进行抑制，无论我们输入了多长时间的过量指令，都不会发生积分饱和，所以不会发生偏航反弹。最棒的一点是，I值饱和不会削弱对低角速度指令的响应——它们将完全不受影响。

当飞行员输入的设定点的变化率（加速度）相对于飞机来说过高时，acc\_limit和acc\_limit\_yaw会选择性地抑制I值积累。默认情况下，它们是关闭的。它们仅仅会在设定点发生快速变化时才会抑制I，这只是暂时性抑制。由于不良的I值抖动可能会在长周期内反复出现，包括在完全不移动摇杆时也会出现，因此不建议使用这两个参数。

###  **以上就是你需要知道的全部内容了……去飞吧！**
