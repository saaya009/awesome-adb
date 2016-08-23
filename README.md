# ADB 用法大全

ADB，即 [Android Debug Bridge](https://developer.android.com/studio/command-line/adb.html)，它是 Android 开发/测试人员不可替代的强大工具，也是 Android 设备玩家的好玩具。

持续更新中，欢迎补充指正。

**注：**有部分命令的支持情况可能与 Android 系统版本及定制 ROM 的实现有关。

## 目录

* [基本用法](#基本用法)
	* [命令语法](#命令语法)
	* [为命令指定目标设备](#为命令指定目标设备)
	* [启动/停止](#启动停止)
* [设备连接管理](#设备连接管理)
	* [查询已连接设备/模拟器](#查询已连接设备模拟器)
	* [USB 连接](#usb-连接)
	* [无线连接](#无线连接)
* [应用管理](#应用管理)
	* [查看应用列表](#查看应用列表)
		* [所有应用](#所有应用)
		* [系统应用](#系统应用)
		* [第三方应用](#第三方应用)
		* [包名包含某字符串的应用](#包名包含某字符串的应用)
	* [安装 APK](#安装-apk)
	* [卸载应用](#卸载应用)
	* [清除应用数据与缓存](#清除应用数据与缓存)
	* [查看前台 Activity](#查看前台-activity)
* [与应用交互](#与应用交互)
	* [调起 Activity](#调起-activity)
	* [调起 Service](#调起-service)
	* [发送广播](#发送广播)
	* [强制停止应用](#强制停止应用)
* [文件管理](#文件管理)
	* [复制设备里的文件到电脑](#复制设备里的文件到电脑)
	* [复制电脑里的文件到设备](#复制电脑里的文件到设备)
* [调试](#调试)
	* [查看/过滤日志](#查看过滤日志)
* [查看设备信息](#查看设备信息)
	* [型号](#型号)
	* [电池状况](#电池状况)
	* [屏幕分辨率](#屏幕分辨率)
	* [屏幕密度](#屏幕密度)
	* [显示屏参数](#显示屏参数)
	* [android\_id](#android_id)
	* [IMEI](#imei)
	* [Android 系统版本](#android-系统版本)
	* [更多硬件与系统属性](#更多硬件与系统属性)
* [实用功能](#实用功能)
	* [屏幕截图](#屏幕截图)
	* [录制屏幕](#录制屏幕)
	* [重新挂载 system 分区为可写](#重新挂载-system-分区为可写)
	* [以 root 权限运行 adbd](#以-root-权限运行-adbd)
	* [查看连接过的 WiFi 密码](#查看连接过的-wifi-密码)
	* [获取设备 Mac 地址](#获取设备-mac-地址)
	* [设置系统日期和时间](#设置系统日期和时间)
	* [重启手机](#重启手机)
* [其它常用 adb shell 命令](#其它常用-adb-shell-命令)
* [参考链接](#参考链接)

## 基本用法

### 命令语法

adb 命令的基本语法如下：

```
adb [-d|-e|-s <serialNumber>] <command>
```

如果只有一个设备/模拟器连接时，可以省略掉 `[-d|-e|-s <serialNumber>]` 这一部分，直接使用 `adb <command>`。

### 为命令指定目标设备

如果有多个设备/模拟器连接，则需要为命令指定目标设备。

| 参数              | 含义                                               |
|-------------------|----------------------------------------------------|
| -d                | 指定当前唯一通过 USB 连接的 Android 设备为命令目标 |
| -e                | 指定当前唯一运行的模拟器为命令目标                 |
| -s <serialNumber> | 指定相应 serialNumber 号的设备/模拟器为命令目标    |

在多个设备/模拟器连接的情况下较常用的是 `-s <serialNumber>` 参数，serialNumber 可以通过 `adb devices` 命令获取。如：

```
$ adb devices

List of devices attached
cf264b8f	device
emulator-5554	device
```

输出里的 `cf264b8f` 和 `emulator-5554` 即为 serialNumber。比如这时想指定 `cf264b8f` 这个设备来运行 adb 命令获取屏幕分辨率：

```
adb -s cf264b8f shell wm size
```

遇到多设备/模拟器的情况均使用这几个参数为命令指定目标设备，下文中为简化描述，不再重复。

### 启动/停止

启动 adb server 命令：

```
adb start-server
```

（一般无需手动执行此命令，在运行 adb 命令时若发现 adb server 没有启动会自动调起。）

停止 adb server 命令：

```
adb kill-server
```

## 设备连接管理

### 查询已连接设备/模拟器

命令：

```
adb devices
```

输出示例：

```
List of devices attached
cf264b8f	device
emulator-5554	device
```

输出格式为 `[serialNumber] [state]`，serialNumber 即我们常说的 SN，state 有如下几种：

* `offline` —— 表示设备未连接成功或无响应。

* `device` —— 设备已连接。注意这个状态并不能标识 Android 系统已经完全启动和可操作，在设备启动过程中设备实例就可连接到 adb，但启动完毕后系统才处于可操作状态。

* `no device` —— 没有设备/模拟器连接。

以上输出显示当前已经连接了两台设备/模拟器，`cf264b8f` 与 `emulator-5554` 分别是它们的 SN。从 `emulator-5554` 这个名字可以看出它是一个 Android 模拟器。

常见异常输出：

1. 没有设备/模拟器连接成功。

   ```
   List of devices attached
   ```

2. 设备/模拟器未连接到 adb 或无响应。

   ```
   List of devices attached
   cf264b8f	offline
   ```

### USB 连接

// TODO

### 无线连接

除了可以通过 USB 连接设备与电脑来使用 adb，也可以通过无线连接——虽然连接过程中也有需要使用 USB 的步骤，但是连接成功之后你的设备就可以在一定范围内摆脱 USB 连接线的限制啦！

操作步骤：

1. 将 Android 设备与将运行 adb 的电脑连接到同一个局域网，比如连到同一个 WiFi。

2. 将设备与电脑通过 USB 线连接。

   应确保连接成功（可运行 `adb devices` 看是否能列出该设备）。

3. 让设备在 5555 端口监听 TCP/IP 连接：

   ```sh
   adb tcpip 5555
   ```

4. 断开 USB 连接。

5. 找到设备的 IP 地址。

   一般能在「设置」-「关于手机」-「状态信息」-「IP地址」找到。

6. 通过 IP 地址连接设备。

   ```sh
   adb connect <device-ip-address>
   ```

   这里的 `<device-ip-address>` 就是上一步中找到的设备 IP 地址。

7. 确认连接状态。

   ```sh
   adb devices
   ```

   如果能看到

   ```sh
   <device-ip-address>:5555 device
   ```

   说明连接成功。

如果连接不了，请确认 Android 设备与电脑是连接到了同一个 WiFi，然后再次执行 `adb connect <device-ip-address>` 那一步；

如果还是不行的话，通过 `adb kill-server` 重新启动 adb 然后从头再来一次试试。

**断开无线连接**

命令：

```
adb disconnect <device-ip-address>
```

## 应用管理

### 查看应用列表

查看应用列表的基本命令格式是

```
adb shell pm list packages [-f] [-d] [-e] [-s] [-3] [-i] [-u] [--user USER_ID] [FILTER]
```

即在 `adb shell pm list packages` 的基础上可以加一些参数进行过滤查看不同的列表，支持的过滤参数如下：

| 参数   | 显示列表                |
|--------|-------------------------|
| 无     | 所有应用                |
| -f     | 显示应用关联的 apk 文件 |
| -d     | 只显示 disabled 的应用  |
| -e     | 只显示 enabled 的应用   |
| -s     | 只显示系统应用          |
| -3     | 只显示第三方应用        |
| -i     | 显示应用的 installer    |
| -u     | 包含已卸载应用          |
| FILTER | 包名包含 FILTER 字符串  |

#### 所有应用

命令：

```
adb shell pm list packages
```

输出示例：

```
package:com.android.smoketest
package:com.example.android.livecubes
package:com.android.providers.telephony
package:com.google.android.googlequicksearchbox
package:com.android.providers.calendar
package:com.android.providers.media
package:com.android.protips
package:com.android.documentsui
package:com.android.gallery
package:com.android.externalstorage
...
// other packages here
...
```

#### 系统应用

命令：

```
adb shell pm list packages -s
```

#### 第三方应用

命令：

```
adb shell pm list packages -3
```

#### 包名包含某字符串的应用

比如要查看包名包含字符串 `mazhuang` 的应用列表，命令：

```
adb shell pm list packages mazhuang
```

当然也可以使用 grep 来过滤：

```
adb shell pm list packages | grep mazhuang
```

### 安装 APK

命令：

```
adb install <apk file>
```

参数：

`adb install` 后面可以跟一些参数来控制安装 APK 的行为，常用参数及含义如下：

| 参数 | 含义                  |
|------|-----------------------|
| -r   | 允许覆盖安装。        |
| -s   | 将应用安装到 sdcard。 |
| -d   | 允许降级覆盖安装。    |

完整参数列表及含义可以直接运行 `adb` 命令然后查看 `adb install [-lrtsdg] <file>` 一节。

如果见到类似如下输出（状态为 `Success`）代表安装成功：

```
12040 KB/s (22205609 bytes in 1.801s)
        pkg: /data/local/tmp/SogouInput_android_v8.3_sweb.apk
Success
```

而如果状态为 `Failure` 则表示安装失败。常见安装失败输出代码、含义及可能的解决办法如下：

| 输出                                               | 含义                                                                     | 解决办法                                        |
|----------------------------------------------------|--------------------------------------------------------------------------|-------------------------------------------------|
| INSTALL\_FAILED\_ALREADY\_EXISTS                   | 应用已经存在                                                             | 使用 `-r` 参数                                  |
| INSTALL\_FAILED\_INVALID\_APK                      | 无效的 APK 文件                                                          |                                                 |
| INSTALL\_FAILED\_INVALID\_URI                      | 无效的 APK 文件名                                                        | 确保 APK 文件名里无中文                         |
| INSTALL\_FAILED\_INSUFFICIENT\_STORAGE             | 空间不足                                                                 | 清理空间                                        |
| INSTALL\_FAILED\_DUPLICATE\_PACKAGE                | 已经存在同名程序                                                         |                                                 |
| INSTALL\_FAILED\_NO\_SHARED\_USER                  | 请求的共享用户不存在                                                     |                                                 |
| INSTALL\_FAILED\_UPDATE\_INCOMPATIBLE              | 已经安装过签名不一样的同名应用，且数据没有移除                           |                                                 |
| INSTALL\_FAILED\_SHARED\_USER\_INCOMPATIBLE        | 请求的共享用户存在但签名不一致                                           |                                                 |
| INSTALL\_FAILED\_MISSING\_SHARED\_LIBRARY          | 安装包使用了设备上不可用的共享库                                         |                                                 |
| INSTALL\_FAILED\_REPLACE\_COULDNT\_DELETE          | 替换时无法删除                                                           |                                                 |
| INSTALL\_FAILED\_DEXOPT                            | dex 优化验证失败或空间不足                                               |                                                 |
| INSTALL\_FAILED\_OLDER\_SDK                        | 设备系统版本低于应用要求                                                 |                                                 |
| INSTALL\_FAILED\_CONFLICTING\_PROVIDER             | 设备里已经存在与应用里同名的 content provider                            |                                                 |
| INSTALL\_FAILED\_NEWER\_SDK                        | 设备系统版本高于应用要求                                                 |                                                 |
| INSTALL\_FAILED\_TEST\_ONLY                        | 应用是 test-only 的，但安装时没有指定 `-t` 参数                          |                                                 |
| INSTALL\_FAILED\_CPU\_ABI\_INCOMPATIBLE            | 包含不兼容设备 CPU 应用程序二进制接口的 native code                      |                                                 |
| INSTALL\_FAILED\_MISSING\_FEATURE                  | 应用使用了设备不可用的功能                                               |                                                 |
| INSTALL\_FAILED\_CONTAINER\_ERROR                  | sdcard 访问失败                                                          | 确认 sdcard 可用，或者安装到内置存储            |
| INSTALL\_FAILED\_INVALID\_INSTALL\_LOCATION        | 不能安装到指定位置                                                       | 切换安装位置，添加或删除 `-s` 参数              |
| INSTALL\_FAILED\_MEDIA\_UNAVAILABLE                | 安装位置不可用                                                           | 一般为 sdcard，确认 sdcard 可用或安装到内置存储 |
| INSTALL\_FAILED\_VERIFICATION\_TIMEOUT             | 验证安装包超时                                                           |                                                 |
| INSTALL\_FAILED\_VERIFICATION\_FAILURE             | 验证安装包失败                                                           |                                                 |
| INSTALL\_FAILED\_PACKAGE\_CHANGED                  | 应用与调用程序期望的不一致                                               |                                                 |
| INSTALL\_FAILED\_UID\_CHANGED                      | 以前安装过该应用，与本次分配的 UID 不一致                                | 清除以前安装过的残留文件                        |
| INSTALL\_FAILED\_VERSION\_DOWNGRADE                | 已经安装了该应用更高版本                                                 | 使用 `-d` 参数                                  |
| INSTALL\_FAILED\_PERMISSION\_MODEL\_DOWNGRADE      | 已安装 target SDK 支持运行时权限的同名应用，要安装的版本不支持运行时权限 |                                                 |
| INSTALL\_PARSE\_FAILED\_NOT\_APK                   | 指定路径不是文件，或不是以 `.apk` 结尾                                   |                                                 |
| INSTALL\_PARSE\_FAILED\_BAD\_MANIFEST              | 无法解析的 AndroidManifest.xml 文件                                      |                                                 |
| INSTALL\_PARSE\_FAILED\_UNEXPECTED\_EXCEPTION      | 解析器遇到异常                                                           |                                                 |
| INSTALL\_PARSE\_FAILED\_NO\_CERTIFICATES           | 安装包没有签名                                                           |                                                 |
| INSTALL\_PARSE\_FAILED\_INCONSISTENT\_CERTIFICATES | 已安装该应用，且签名与 APK 文件不一致                                    | 先卸载设备上的该应用，再安装                    |
| INSTALL\_PARSE\_FAILED\_CERTIFICATE\_ENCODING      | 解析 APK 文件时遇到 `CertificateEncodingException`                       |                                                 |
| INSTALL\_PARSE\_FAILED\_BAD\_PACKAGE\_NAME         | manifest 文件里没有或者使用了无效的包名                                  |                                                 |
| INSTALL\_PARSE\_FAILED\_BAD\_SHARED\_USER\_ID      | manifest 文件里指定了无效的共享用户 ID                                   |                                                 |
| INSTALL\_PARSE\_FAILED\_MANIFEST\_MALFORMED        | 解析 manifest 文件时遇到结构性错误                                       |                                                 |
| INSTALL\_PARSE\_FAILED\_MANIFEST\_EMPTY            | 在 manifest 文件里找不到找可操作标签（instrumentation 或 application）   |                                                 |
| INSTALL\_FAILED\_INTERNAL\_ERROR                   | 因系统问题安装失败                                                       |                                                 |
| INSTALL\_FAILED\_USER\_RESTRICTED                  | 用户被限制安装应用                                                       |                                                 |
| INSTALL\_FAILED\_DUPLICATE\_PERMISSION             | 应用尝试定义一个已经存在的权限名称                                       |                                                 |
| INSTALL\_FAILED\_NO\_MATCHING\_ABIS                | 应用包含设备的应用程序二进制接口不支持的 native code                     |                                                 |
| INSTALL\_CANCELED\_BY\_USER                        | 应用安装需要在设备上确认，但未操作设备或点了取消                         | 在设备上同意安装                                |
| INSTALL\_FAILED\_ACWF\_INCOMPATIBLE                | 应用程序与设备不兼容                                                     |                                                 |
| does not contain AndroidManifest.xml               | 无效的 APK 文件                                                          |                                                 |
| is not a valid zip file                            | 无效的 APK 文件                                                          |                                                 |
| Offline                                            | 设备未连接成功                                                           | 先将设备与 adb 连接成功                         |
| unauthorized                                       | 设备未授权允许调试                                                       |                                                 |
| error: device not found                            | 没有连接成功的设备                                                       | 先将设备与 adb 连接成功                         |
| protocol failure                                   | 设备已断开连接                                                           | 先将设备与 adb 连接成功                         |
| Unknown option: -s                                 | Android 2.2 以下不支持安装到 sdcard                                      | 不使用 `-s` 参数                                |
| No space left on devicerm                          | 空间不足                                                                 | 清理空间                                        |
| Permission denied ... sdcard ...                   | sdcard 不可用                                                            |                                                 |

参考：[PackageManager.java](https://github.com/android/platform_frameworks_base/blob/master/core%2Fjava%2Fandroid%2Fcontent%2Fpm%2FPackageManager.java)

### 卸载应用

命令：

```
adb uninstall [-k] <packagename>
```

`<packagename>` 表示应用的包名，`-k` 参数可选，表示卸载应用但保留数据和缓存目录。

命令示例：

```
adb uninstall com.qihoo360.mobilesafe
```

表示卸载 360 手机卫士。

### 清除应用数据与缓存

命令：

```
adb shell pm clear <packagename>
```

`<packagename>` 表示应用名包，这条命令的效果相当于在设置里的应用信息界面点击了「清除缓存」和「清除数据」。

命令示例：

```
adb shell pm clear com.qihoo360.mobilesafe
```

表示清除 360 手机卫士的数据和缓存。

### 查看前台 Activity

命令：

```
adb shell dumpsys activity activities | grep mFocusedActivity
```

输出示例：

```
mFocusedActivity: ActivityRecord{8079d7e u0 com.cyanogenmod.trebuchet/com.android.launcher3.Launcher t42}
```

其中的 `com.cyanogenmod.trebuchet/com.android.launcher3.Launcher` 就是当前处于前台的 Activity。

## 与应用交互

### 调起 Activity

// TODO

### 调起 Service

// TODO

### 发送广播

// TODO

### 强制停止应用

命令：

```
adb shell am force-stop <packagename>
```

命令示例：

```
adb shell am force-stop com.qihoo360.mobilesafe
```

表示停止 360 安全卫士的一切进程与服务。

## 文件管理

### 复制设备里的文件到电脑

命令：

```
adb pull <设备里的文件路径> [电脑上的目录] 
```

其中 `电脑上的目录` 参数可以省略，默认复制到当前目录。

例：

```
adb pull /sdcard/sr.mp4 ~/tmp/
```

*小技巧：*设备上的文件路径可能需要 root 权限才能访问，如果你的设备已经 root 过，可以先使用 `adb shell` 和 `su` 命令在 adb shell 里获取 root 权限后，先 `cp /path/on/device /sdcard/filename` 将文件复制到 sdcard，然后 `adb pull /sdcard/filename /path/on/pc`。

### 复制电脑里的文件到设备

命令：

```
adb push <电脑上的文件路径> <设备里的目录> 
```

例：

```
adb push ~/sr.mp4 /sdcard/
```

*小技巧：*设备上的文件路径普通权限可能无法直接写入，如果你的设备已经 root 过，可以先 `adb push /path/on/pc /sdcard/filename`，然后 `adb shell` 和 `su` 在 adb shell 里获取 root 权限后，`cp /sdcard/filename /path/on/device`。

## 调试

### 查看/过滤日志

// TODO

## 查看设备信息

### 型号

命令：

```
adb shell getprop ro.product.model
```

输出示例：

```
Nexus 5
```

### 电池状况

命令：

```
adb shell dumpsys battery
```

输入示例：

```
Current Battery Service state:
  AC powered: false
  USB powered: true
  Wireless powered: false
  status: 2
  health: 2
  present: true
  level: 44
  scale: 100
  voltage: 3872
  temperature: 280
  technology: Li-poly
```

其中 `scale` 代表最大电量，`level` 代表当前电量。上面的输出表示还剩下 44% 的电量。

### 屏幕分辨率

命令：

```
adb shell wm size
```

输出示例：

```
Physical size: 1080x1920
```

该设备屏幕分辨率为 1080px * 1920px。

### 屏幕密度

命令：

```
adb shell wm density
```

输出示例：

```
Physical density: 420
```

该设备屏幕密度为 420dpi。

### 显示屏参数

命令：

```
adb shell dumpsys window displays
```

输出示例：

```
WINDOW MANAGER DISPLAY CONTENTS (dumpsys window displays)
  Display: mDisplayId=0
    init=1080x1920 420dpi cur=1080x1920 app=1080x1794 rng=1080x1017-1810x1731
    deferred=false layoutNeeded=false
```

其中 `mDisplayId` 为 显示屏编号，`init` 是初始分辨率和屏幕密度，`app` 的高度比 `init` 里的要小，表示屏幕底部有虚拟按键，高度为 1920 - 1794 = 126px 合 42dp。

### android\_id

命令：

```
adb shell settings get secure android_id
```

输出示例：

```
51b6be48bac8c569
```

### IMEI

在 Android 4.4 及以下版本可通过如下命令获取 IMEI：

```
adb shell dumpsys iphonesubinfo
```

输出示例：

```
Phone Subscriber Info:
  Phone Type = GSM
  Device ID = 860955027785041
```

其中的 `Device ID` 就是 IMEI。

而在 Android 5.0 及以上版本里这个命令输出为空，得通过其它方式获取了（需要 root 权限）：

```
adb shell
su
service call iphonesubinfo 1
```

输出示例：

```
Result: Parcel(
  0x00000000: 00000000 0000000f 00360038 00390030 '........8.6.0.9.'
  0x00000010: 00350035 00320030 00370037 00350038 '5.5.0.2.7.7.8.5.'
  0x00000020: 00340030 00000031                   '0.4.1...        ')
```

把里面的有效内容提取出来就是 IMEI 了，比如这里的是 `860955027785041`。

参考：[adb shell dumpsys iphonesubinfo not working since Android 5.0 Lollipop](http://stackoverflow.com/questions/27002663/adb-shell-dumpsys-iphonesubinfo-not-working-since-android-5-0-lollipop)

### Android 系统版本

命令：

```
adb shell getprop ro.build.version.release
```

输出示例：

```
5.0.2
```

### 更多硬件与系统属性

设备的更多硬件与系统属性可以通过如下命令查看：

```
adb shell cat /system/build.prop
```

这会输出很多信息，包括前面几个小节提到的「型号」和「Android 系统版本」等。

输出里还包括一些其它有用的信息，它们也可通过 `adb shell getprop <属性名>` 命令单独查看，列举一部分属性如下：

| 属性名                          | 含义                     |
|---------------------------------|--------------------------|
| ro.build.version.sdk            | SDK 版本                 |
| ro.build.version.release        | Android 系统版本         |
| ro.build.version.security_patch | Android 安全补丁程序级别 |
| ro.product.model                | 型号                     |
| ro.product.brand                | 品牌                     |
| ro.product.name                 | 设备名                   |
| ro.product.board                | 处理器型号               |
| ro.product.cpu.abilist          | CPU 支持的 abi 列表      |
| persist.sys.isUsbOtgEnabled     | 是否支持 OTG             |
| dalvik.vm.heapsize              | 每个应用程序的内存上限   |
| ro.sf.lcd_density               | 屏幕密度                 |

## 实用功能

### 屏幕截图

命令：

```
adb shell screencap -p /sdcard/sc.png
```

然后将 png 文件导出到电脑：

```
adb pull /sdcard/sc.png
```

可以使用 `adb shell screencap -h` 查看 `screencap` 命令的帮助信息，下面是两个有意义的参数及含义：

| 参数          | 含义                                       |
|---------------|--------------------------------------------|
| -p            | 指定保存文件为 png 格式                    |
| -d display-id | 指定截图的显示屏编号（有多显示屏的情况下） |

实测如果指定文件名以 `.png` 结尾时可以省略 -p 参数；否则需要使用 -p 参数。如果不指定文件名，截图文件的内容将直接输出到 stdout。

### 录制屏幕

录制屏幕以 mp4 格式保存到 /sdcard：

```
adb shell screenrecord /sdcard/filename.mp4
```

需要停止时按 <kbd>Ctrl-C</kbd>，默认录制时间和最长录制时间都是 180 秒。

如果需要导出到电脑：

```
adb pull /sdcard/filename.mp4
```

可以使用 `adb shell screenrecord --help` 查看 `screenrecord` 命令的帮助信息，下面是常见参数及含义：

| 参数                | 含义                                            |
|---------------------|-------------------------------------------------|
| --size WIDTHxHEIGHT | 视频的尺寸，比如 `1280x720`，默认是屏幕分辨率。 |
| --bit-rate RATE     | 视频的比特率，默认是 4Mbps。                    |
| --time-limit TIME   | 录制时长，单位秒。                              |
| --verbose           | 输出更多信息。                                  |

### 重新挂载 system 分区为可写

**注：需要 root 权限。**

/system 分区默认挂载为只读，但有些操作比如给 Android 系统添加命令、删除自带应用等需要对 /system 进行写操作，所以需要重新挂载它为可读写。

步骤：

1. 进入 shell 并切换到 root 用户权限。

   命令：

   ```sh
   adb shell
   su
   ```

2. 查看当前分区挂载情况。

   命令：

   ```sh
   mount
   ```

   输出示例：

   ```sh
   rootfs / rootfs ro,relatime 0 0
   tmpfs /dev tmpfs rw,seclabel,nosuid,relatime,mode=755 0 0
   devpts /dev/pts devpts rw,seclabel,relatime,mode=600 0 0
   proc /proc proc rw,relatime 0 0
   sysfs /sys sysfs rw,seclabel,relatime 0 0
   selinuxfs /sys/fs/selinux selinuxfs rw,relatime 0 0
   debugfs /sys/kernel/debug debugfs rw,relatime 0 0
   none /var tmpfs rw,seclabel,relatime,mode=770,gid=1000 0 0
   none /acct cgroup rw,relatime,cpuacct 0 0
   none /sys/fs/cgroup tmpfs rw,seclabel,relatime,mode=750,gid=1000 0 0
   none /sys/fs/cgroup/memory cgroup rw,relatime,memory 0 0
   tmpfs /mnt/asec tmpfs rw,seclabel,relatime,mode=755,gid=1000 0 0
   tmpfs /mnt/obb tmpfs rw,seclabel,relatime,mode=755,gid=1000 0 0
   none /dev/memcg cgroup rw,relatime,memory 0 0
   none /dev/cpuctl cgroup rw,relatime,cpu 0 0
   none /sys/fs/cgroup tmpfs rw,seclabel,relatime,mode=750,gid=1000 0 0
   none /sys/fs/cgroup/memory cgroup rw,relatime,memory 0 0
   none /sys/fs/cgroup/freezer cgroup rw,relatime,freezer 0 0
   /dev/block/platform/msm_sdcc.1/by-name/system /system ext4 ro,seclabel,relatime,data=ordered 0 0
   /dev/block/platform/msm_sdcc.1/by-name/userdata /data ext4 rw,seclabel,nosuid,nodev,relatime,noauto_da_alloc,data=ordered 0 0
   /dev/block/platform/msm_sdcc.1/by-name/cache /cache ext4 rw,seclabel,nosuid,nodev,relatime,data=ordered 0 0
   /dev/block/platform/msm_sdcc.1/by-name/persist /persist ext4 rw,seclabel,nosuid,nodev,relatime,data=ordered 0 0
   /dev/block/platform/msm_sdcc.1/by-name/modem /firmware vfat ro,context=u:object_r:firmware_file:s0,relatime,uid=1000,gid=1000,fmask=0337,dmask=0227,codepage=cp437,iocharset=iso8859-1,shortname=lower,errors=remount-ro 0 0
   /dev/fuse /mnt/shell/emulated fuse rw,nosuid,nodev,relatime,user_id=1023,group_id=1023,default_permissions,allow_other 0 0
   /dev/fuse /mnt/shell/emulated/0 fuse rw,nosuid,nodev,relatime,user_id=1023,group_id=1023,default_permissions,allow_other 0 0
   ```

   找到其中我们关注的带 /system 的那一行：

   ```sh
   /dev/block/platform/msm_sdcc.1/by-name/system /system ext4 ro,seclabel,relatime,data=ordered 0 0
   ```

3. 重新挂载。

   命令：

   ```sh
   mount -o remount,rw -t yaffs2 /dev/block/platform/msm_sdcc.1/by-name/system /system
   ```

   这里的 `/dev/block/platform/msm_sdcc.1/by-name/system` 就是我们从上一步的输出里得到的文件路径。

如果输出没有提示错误的话，操作就成功了，可以对 /system 下的文件为所欲为了。

### 以 root 权限运行 adbd

adb 的运行原理是 PC 端的 adb server 与手机端的守护进程 adbd 建立连接，然后 PC 端的 adb client 通过 adb server 转发命令，adbd 接收命令后解析运行。

所以如果 adbd 以普通权限执行，有些需要 root 权限才能执行的命令无法直接用 `adb xxx` 执行。这时可以 `adb shell` 然后 `su` 后执行命令，也可以让 adbd 以 root 权限执行，这个就能随意执行高权限命令了。

命令：

```sh
adb root
```

正常输出：

```sh
restarting adbd as root
```

现在再运行 `adb shell`，看看命令行提示符是不是变成 `#` 了？

有些手机 root 后也无法通过 `adb root` 命令让 adbd 以 root 权限执行，比如三星的部分机型，会提示 `adbd cannot run as root in production builds`，此时可以先安装 adbd Insecure，然后 `adb root` 试试。

### 查看连接过的 WiFi 密码

**注：需要 root 权限。**

命令：

```sh
adb shell
su
cat /data/misc/wifi/*.conf
```

输出示例：

```sh
network={
	ssid="TP-LINK_9DFC"
	scan_ssid=1
	psk="123456789"
	key_mgmt=WPA-PSK
	group=CCMP TKIP
	auth_alg=OPEN
	sim_num=1
	priority=13893
}

network={
	ssid="TP-LINK_F11E"
	psk="987654321"
	key_mgmt=WPA-PSK
	sim_num=1
	priority=17293
}
```

`ssid` 即为我们在 WLAN 设置里看到的名称，`psk` 为密码，`key_mgmt` 为安全加密方式。

### 获取设备 Mac 地址

命令：

```sh
adb shell cat /sys/class/net/wlan0/address
```

输出示例：

```sh
f8:a9:d0:17:42:4d
```

### 设置系统日期和时间

**注：需要 root 权限。**

命令：

```sh
adb shell
su
date -s 20160823.131500
```

表示将系统日期和时间更改为 2016 年 08 月 23 日 13 点 15 分 00 秒。

### 重启手机

命令：

```sh
adb reboot
```

## 其它常用 adb shell 命令

Android 系统是基于 Linux 内核的，所以 Linux 里的很多命令在 Android 里也有相同或类似的实现，在 `adb shell` 里可以调用。本文档前面的部分内容已经用到了 `adb shell` 命令。如下是常用命令的简单描述，前文已经专门讲过的命令不再列举：

| 命令  | 功能                        |
|-------|-----------------------------|
| cat   | 显示文件内容                |
| cd    | 切换目录                    |
| chmod | 改变文件的存取模式/访问权限 |
| grep  | 过滤输出                    |
| ls    | 列举目录内容                |
| mount | 挂载目录的查看和管理        |
| mv    | 移动或重命名文件            |
| ps    | 查看正在运行的进程          |
| top   | 查看进程的资源占用情况      |

## 参考链接

* [Android Debug Bridge](https://developer.android.com/studio/command-line/adb.html)
* [ADB Shell Commands](https://developer.android.com/studio/command-line/shell.html)
* [logcat Command-line Tool](https://developer.android.com/studio/command-line/logcat.html)
* [Android ADB命令大全](http://zmywly8866.github.io/2015/01/24/all-adb-command.html)
