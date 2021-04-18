# GPS救援模式

## 这个模式能：

GPS救援模式用于在紧急情况（如图传链路或遥控链路丢失）时自动将飞机飞回。**此功能唯一的目的是将飞机飞回一定范围内，以便您可以尽快重新控制飞机。**这不是一个可靠的返航功能。请牢记这一点，并且仅在为了避免丢失飞机时才激活此模式（首次测试除外）。为了提高GPS救援的可靠性，请仔细阅读此文档，并依照飞行环境与飞行习惯尽可能好地配置系统。

## **这个模式不能:**

GPS救援不是一个完整的返航功能。这意味着它不能自动将您的飞机飞回家，而且您也不应该尝试这样做。它没有自动降落的功能，并且会在靠近返航点的时候“轻轻地摔在地上”。此功能的唯一目的是在信号丢失的情况下将飞机飞回来一点，以便

## 需要条件

* GPS。推荐使用Ublox M8N模块的变体。此功能已在18x18mm的M8N模块和北天BN880和其他类似型号上完成了测试。
* **必须启用并正确校准加速度计。**GPS救援需要使用它以使飞机保持水平。
* 气压计是可选项，但推荐使用。板载气压计和外置气压计（I2C）均已通过我们的测试，可以正常使用。
* 此模式并不需要罗盘，但如果可用则会使用。
* 不支持3D模式。如果开启了3D模式，则GPS救援会被禁用。

## 免责声明

* 这是一个实验性的功能。
* 请小心使用。
* 此文档会经常更改，所以请经常来查看它。
* 除特殊说明外，此文档与最新的稳定版固件相配套。
* 如果想将此模式用于失控保护，则应启用沙盒自检功能。

## 必备条件



## 设置GPS救援


为了在飞机上设置GPS救援，我们强烈推荐从头开始进行完整的设置过程。此步骤旨在受控环境中联系，并充分理解GPS救援的运行逻辑及短板。使用现成的GPS救援设置（复制他人的设置）会降低救援成功的几率。

#### 在Betaflight“模式”页面为GPS救援模式添加一个控制通道。确认此模式可被正常激活（测试前请卸除螺旋桨）。

在CLI中设置如下参数：

`set gps_rescue_initial_alt=[number] (默认为50)`

这是最重要的参数。当GPS救援被激活，飞机将朝向起飞点，并尝试爬升至一个安全高度。这个高度可以在这里设置，也可以是飞行过程中记录到的的最大高度+15米，并取二者中的高者。我个人习惯将其设置为70或80米。

`set gps_rescue_ground_speed=[number] (默认为2000)`

这是飞机返航时的速度，以厘米/秒为单位。我习惯设为1500（约35mph，54km/h），此设置取决于具体的飞行情况。

`set gps_rescue_angle=[number] (默认为32)`

这是返航时的最大倾斜角度。这项设置可以防止飞机到达全速，所以在更改默认设置时最好进行测试。注意，角度越大，飞机就越难保持稳定的高度。由于有可能遇上返航时逆风的情况，我习惯把该参数设置为45。

`set gps_rescue_descent_dist =[number] (默认为200)`

这是飞机距起飞点多远开始下降的距离，以米为单位。

`set gps_rescue_ascend_rate = [number] (默认为500)` \(在BF4.1中加入\)

这是飞机爬升时的垂直速度，以厘米/秒为单位。

`set gps_rescue_descend_rate = [number] (默认为150)` \(在BF4.1中加入\)

这是飞机下降的垂直速度，以厘米/秒为单位。

`gps_rescue_throttle_min` 和 `gps_rescue_throttle_max` 在BF4.1中仅用于限制新PID控制器的干预\([https://github.com/betaflight/betaflight/pull/8015](https://github.com/betaflight/betaflight/pull/8015)\)

`gps_rescue_alt_mode = [MAX_ALT, FIXED_ALT, CURRENT_ALT]` \(在BF4.1中加入\)

现在我们可以设置GPS救援的高度了。

**MAX\_ALT是默认的设置，高度就是**`gps_rescue_initial_alt，`**或者飞行过程中记录到的最大高度+15米。**

**FIXED\_ALT，**飞机总是尝试保持设置的高度值。\(`gps_rescue_initial_alt`\)

**CURRENT\_ALT，** 飞机保持触发GPS救援时的高度（不建议使用）。

#### 现在可以去测试GPS救援了。

等待您的GPS模块获得良好的定位。默认情况下，如果卫星数少于`gps_rescue_min_sats` \(默认是8\)，飞机将无法被解锁。

### 我们建议按如下步骤进行测试。

沿着直线飞到比开始下降距离再远100米的地方。例如，如果开始下降距离设置为150米，那就飞250米。当保持直线飞行时，返航箭头应一直指向返航点。

#### 非常重要：如果箭头没有指向返航点，请勿激活GPS救援。如果此时激活了GPS救援，飞机将飞向箭头所指的方向。

激活GPS救援。

#### 重要：请做好飞机不向回飞的时候关闭GPS救援，并夺回控制权的准备

如果一切顺利，飞机会返回并开始下降。不要让飞机离地面或自己太近，因为目前的程序并不包括降落功能。飞机会直接在附近坠毁。

你可能会注意到，飞机很难保持稳定的高度。有时，这会在GPS高度读数不稳定时发生，因为高度控制器的期望值总在不断变化。如果GPS高度读数很稳定，但是飞机仍不能稳定在设定值附近10米的范围内，就需要调整高度油门的PID增益了。以下是这些参数：

`gps_rescue_throttle_P`    
`gps_rescue_throttle_I   
gps_rescue_throttle_D`

我们不希望大多数人都会精调返航速度增益，默认情况下的PID增益是：

`gps_rescue_velocity_P = 80  
gps_rescue_velocity_I = 10  
gps_rescue_velocity_D = 20`

当飞机可靠地返航一次之后，就可以逐步加大测试的距离和范围。当配置可靠的时候，就可以将GPS救援设置为失控保护。
`set failsafe_procedure = GPS-RESCUE`

完成上面这个设置后，GPS救援将在失控时被激活。

v4.0.x备注 - 当遥控信号恢复时，飞手将立即获得控制权。所以在失控时，飞手应手动激活GPS救援模式，这样就可以意外的控制权切换，或者做好在任何时刻接管控制的准备。我们推荐使用前者，这需要在将GPS救援用于失控保护的同时为其分配一个开关。

v4.1和更高版本 - 当遥控信号恢复后，仅当roll/pitch/yaw三个轴的杆量输入之和超过`failsafe_stick_threshold`的百分比值时，失控保护才会被退出，飞手才将获得控制权。该参数的默认值是30，意味着roll，pitch，yaw需要从中点移动至少30%失控保护才会关闭。如果要减少切换过程的突变性，可以选择降低这个值。

另外，可以在配置程序中“失控保护”页面（需开启专家模式）配置失控保护，也可以使用OSD菜单\(`FEATURES > FAILSAFE`\)来设置。OSD界面也可以很方便的在有限空间（如室内或树林中）飞行时禁用GPS救援功能。请确认在合适的时候将其重新启用。

## 处理错误情况/系统自检（非常重要）



`set gps_rescue_sanity_checks = RESCUE_SANITY_ON`

如果想让系统自检仅在失控时启用，也可以将其设为`RESCUE_SANITY_FS_ONLY`

系统自检可以确保：

* GPS仍然连接在飞控上
* GPS的位置读数有效
* 飞机并未遭受剧烈冲击（由碰撞造成）
* 卫星数量不少于设定值
* 在到达初始高度之后，飞机开始靠近返航点

如果不满足任一条件，那么GPS救援操作将被终止，这意味着飞机将直接坠落。但是，最后两个条件在触发之后有几秒钟的容许时间。同样地，如果飞机没有开始靠近返航点，而且磁力计已被占用，GPS救援则会使用GPS的方向读数来作为第二次尝试。如果仍然检测到了飞机正在飞向错误的方向，GPS救援操作仍将被终止。

## 定位成功前解锁

默认情况下，如果已经为GPS救援配置了一个拨杆开关或将其作为失控保护程序，那么Betaflight在没有完成GPS定位前不会允许解锁。某些情况下，您可能会希望在没有完成GPS定位的情况下起飞（也许当地的GPS信号覆盖很差，或想在搜星时快速飞一圈），但又不想直接禁用GPS救援，那么可以通过以下命令实现：

`set gps_rescue_allow_arming_without_fix = ON`

开启此选项后，在没有GPS定位的情况下也能起飞，**但是GPS救援会在此次飞行中被临时禁用**。一条警告信息（RESCUE OFF）会显示在OSD上。如果在飞行中搜够了卫星数量，用“降落、锁定，再次解锁”的方法，开启GPS救援。

## GPS救援不可用的情况

如果为GPS救援配置了一个是拨杆开关或将其用于失控保护，飞控会持续检测一些参数的状态（ GPS与飞控的连接情况、有效的GPS定位、最少卫星数量）。如果以上条件有任意一项不满足，一条警告信息（RESCUE OFF）会显示在OSD上。这只是一个警告，如果此时GPS救援被激活，仍会正常进行系统自检。这条警告并不影响系统自检以及后续的动作。

## 常见问题

* 在测试GPS救援前，确保已飞出最小距离（默认是100米）的范围，这个最小距离可以在CLI中使用`set gps_rescue_min_dth = <meters>`设置。在最小距离之内，GPS救援将导致飞机坠毁。
* 在一些特殊情况下加速度计会逐渐发生偏离，导致GPS救援无法正常工作。检查方法是飞行一段较长的时间，然后切换至自稳模式。如果飞机无法稳定的保持水平，请勿在这架飞机上使用GPS救援功能。
* 一些GPS模块需要对U-Center进行配置才能工作。参考这个由Painless360制作的[视频](https://www.youtube.com/watch?v=8FIi_xuH4Vo)进行设置。
* GPS救援功能仍在持续开发，所以如果不在使用最新的稳定版BF固件，有几率会遇到一些故障情况。请确保使用的是最新的稳定版固件。
*  GPS救援不是也不会是完全可靠的。在OSD上显示GPS经纬度并用DVR录制飞行过程，在遥控器上记录遥测数据（包括GPS坐标），或者使用自动启动的蜂鸣器，这些是应该在测试GPS救援之前就使用的方法。

### 老版本的常见问题

* 对于早于BF4.0的版本，非常推荐启用Air Mode，选择性的精调一阶失控保护，作为GPS救援后的碰撞检测的应对方法。至少要确保飞机不会在进入二阶失控保护时直接自由落体。当使用等于或低于10.4版本的地面站更改失控保护设置时，失控保护的方式会被重置。在“失控保护”页面保存更改后，再去CLI手动设置失控保护的方式。
* 每次解锁时，返航点都会刷新。但在早于BF4.0的版本中，返航点是在锁定时刷新的，快速拨动解锁开关可能会导致刷新失败。在所有版本中，最好的做法是解锁并等待几秒钟，直到OSD中显示返航点距离为0米，然后再开始飞行。否则，锁定并等待几秒后再重复此操作。从BF4.0开始，可以使用此命令`set gps_set_home_point_once = ON`使返航点仅为上电后首次解锁时的位置
* 如果使用黑羊接收机，请在“CROSSFIRE RX”菜单中设置失控保护行为为“Cut”。

## 版本历史

**Betaflight 4.1**

* 当GPS救援作为失控保护被激活时，使用摇杆以恢复控制。\([https://github.com/betaflight/betaflight/pull/7936](https://github.com/betaflight/betaflight/pull/7936)\)

**Betaflight 4.0**

* 防止在进入GPS救援模式时立即触发碰撞检测。\([https://github.com/betaflight/betaflight/pull/7034](https://github.com/betaflight/betaflight/pull/7034)\)
* 允许更改与返航点的最小距离。\([https://github.com/betaflight/betaflight/pull/6404](https://github.com/betaflight/betaflight/pull/6404)\)
* 修复在初始阶段系统自检误报STALLED/FLYAWAY
* \([https://github.com/betaflight/betaflight/pull/7254](https://github.com/betaflight/betaflight/pull/7254)\)
* 增加了用于标明GPS救援功能不可靠的标识。\([https://github.com/betaflight/betaflight/pull/7256](https://github.com/betaflight/betaflight/pull/7256)\)
* 增加了在没有GPS定位或卫星数量不足时允许解锁的明确设置项：gps\_rescue\_allow\_arming\_without\_fix \([https://github.com/betaflight/betaflight/pull/7320](https://github.com/betaflight/betaflight/pull/7320)\)

**Betaflight 3.5.5**

* 修复在初始阶段系统自检误报STALLED。 \([https://github.com/betaflight/betaflight/pull/7254](https://github.com/betaflight/betaflight/pull/7254)\)

**Betaflight 3.5.3**

* 修复当“解锁时不转动电机”开始时在激活GPS救援时自动锁定的现象。\([https://github.com/betaflight/betaflight/pull/6979](https://github.com/betaflight/betaflight/pull/6979)\);

**Betaflight 3.5**

* 被失控保护激活的GPS救援将检查飞机距返航点是远于100米，无论系统自检是否打开。如果距返航点100米以内，飞机将直接坠落。非失控保护激活的GPS救援仍按3.4版本运作。

**Betaflight 3.4**

* 系统自检包括一个在GPS救援激活时，飞机距返航点不少于100米的检查。当系统自检被打开，如果GPS激活（手动或被失控保护）时飞机距返航点100米以内，飞机将直接坠落。
