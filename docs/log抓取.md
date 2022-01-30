# log抓取
\*\*

# 一.常见log抓取命令
## 1.logcat
     adb logcat   >  ./logcat/（user ,userdeug,factory都适用，user版本log少一点）

## 2.bugreport
     adb bugreport >./bugreport.zip（user ,userdeug,factory都适用）

## 3.aplogd
    adb pull data/vendor/aplogd ./aplogd/（userdebug factory适用，user版本需刷token和对应镜像）

## 4.logfs
    fastboot oem partition dump logfs（都适用）

## 5.bug2Go
   apk链接：

    https://artifacts-bjmirr.mot.com/artifactory/webapp/#/artifacts/browse/simple/General/apps/DF-apks/latest/

 adb install  xxx.apk（都适用，aplog需打开app里开关）

## 6.制作token需要的信息
fastboot devices

fastboot getvar token（看有没有failed，没有fail选V2，有fail选V1）

fastboot getvar imei

fastboot getvar uid



# 二.non secure  user版本
刷user版本的boot-debug.img

fastboot flash boot boot-debug.img

fastboot flash vendor\_boot vendor\_boot-debug.img 

fastboot oem config cmdl "androidboot.verifiedbootstate=orange"

# 三.secure  factory版本
可以脚本直接抓，脚本链接

https://drive.google.com/file/d/1vqSMt\_B-evjaiMCDmbQ8o\_cfr2Fmvuwg/view?usp=sharing

# 四.secure user版本
1.抓aplog

   fastboot flash debug\_token xxxx.tkn

   fastboot reboot bootloader

   fastboot flash boot boot-debug.img

   fastboot flash vendor\_boot vendor\_boot-debug.img 

   fastboot oem config cmdl "androidboot.verifiedbootstate=orange"

   fastboot reboot

  adb root

  adb shell setprop persist.vendor.log.aplogd.enable 1

  复现后

   adb pull /data/vendor/aplogd  ./aplogd/



2.输tcmd指令

   1>radiocomm输

    fastboot oem cid\_prov\_req  factory\_auth

    fastboot oem config bootmode factory

    fastboot continue

    fastboot reboot

   2>adb 里输

   比如：

      start vendor.tcmd

       tcmd send  “ XXX“

    刷完token后刷userdebug包里的镜像

     fastboot getvar current-slot  

     结果若不是slot a  ，执行fastboot set\_active a

     qcom:

      fastboot flash vbmeta vbmeta.img

      fastboot flash logo logo.bin

      fastboot flash boot boot.img

      fastboot flash vendor\_boot vendor\_boot.img

     fastboo flash dtbo dtbo.img

     fastboot flash vbmeta\_system vbmeta\_system.img

     fastboot flash super super.img 

   mtk：

     fastboot flash vbmeta vbmeta.img

     fastboot flash vbmeta\_system vbmeta\_system.img

     fastboot flash boot boot.img

     fastboot flash super super.img 

     fastboot reboot

执行完上面的再执行

  adb reboot bootloader

  fastboot oem config cmdl “androidboot.verifiedbootstate=orange”

  fastboot oem ssm disable-verity

  fastboot -w

  fastboot reboot 

  adb root

  adb remount



