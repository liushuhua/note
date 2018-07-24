# Android 文件系统

## 系统目录

* /system/app

    预载入应用程式执行档(*.apk)，都是放在这。像是Alarm Clock, Browser,
    Contacts, Maps等系统应用。

* /system/framework

    这里放 Android 系统的核心程式库。像是core.jar, framework-res.apk,
    com.google.android.gtalkservice.jar,...等等。
    **备注:** 虽然许多程式库都是以jar 结尾的，不过里面Java classes 还是
    以dex 格式存在着。

* /system/media/audio/(notification, alarms, ringtones, ui)

    这里放系统的声音档，像是闹铃声，来电铃声等等。这些声音档，多是 ogg 格式。

*  /data/anr/traces.txt

    记录手机发生ANR的信息文件

* /data/app

    这里放的是使用者自己安装的应用程式执行档(*.apk)。 __(对应：system/app)__

* /data/data/<app-package-name>

    当你在程式中用Context.openFileOutput() 所建立的档案，都放在这个目录下
    的files 子目录内。而用Context.getSharedPreferences() 所建立的preferences
    档(*.xml) ，则是放在shared_pref 这个子目录中。Cache App缓存目录，database
    APP数据库文件（应用私有空间）

## 系统本地程序目录

* \system\bin

    这个目录下的文件都是系统的本地程序，从bin文件夹名称可以看出是binary
    二进制的程序，里面主要是Linux系统自带的组件，Android手机网就主要文件:

    1. \system\bin\am ---------------------ActivityManager
    2. \system\bin\app_process --------系统进程
    3. \system\bin\dumpstate  -----------状态抓取器
    4. \system\bin\dumpsys  -------------系统抓取器
    5. \system\bin\installd  ----------------安装
    6. \system\bin\logcat  -----------------日志打印
    7. \system\bin\mountd  ---------------存储挂载器
    8. \system\bin\mountd  ---------------存储挂载器
    9. \system\bin\pm    ---------------------包管理器

* \system\etc

    从文件夹名称来看保存的都是系统的配置文件，比如APN接入点设置等核心配置。

* \system\fonts

    字体文件夹

* \system\lib

    lib目录中存放的主要是系统底层库，如平台运行时库。

* \system\usr

  用户文件夹，包含共享、键盘布局、时间区域文件等。
