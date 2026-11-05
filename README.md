# Uperf

An Android user-space performance controller that implements most kernel-space upscaling functions and supports more scenario recognition.

## Main Features

- Dynamically set parameters to control performance release based on the identified scenario type, supporting all `sysfs` nodes.
- Supports dynamically binding the UI-related threads of the currently operating app to the high-performance cluster.
- Reads Android touchscreen input signals from the Linux layer, recognizing clicks and swipes.
- Actively samples system load to identify heavy loads, such as during app startup.
- Monitors cpuset group update operations to identify app switching.
- Monitors wake lock update operations to identify whether the screen is off.
- Monitors notifications sent by hooks injected into Surfaceflinger to identify rendering start, lag, and end.
- Supports Android 8.0-16.
- Supports arm64-v8a.
- Supports most, if not all, Root Managers.
- Compatible only with Snapdragon SoCs that use Scheduler EAS.
- SELinux maintains `enforcing` in most cases unless SfAnalysis injection fails.
- SfAnalysis is optional; only use it if your device is compatible (i.e., doesn't freeze on the boot screen). With SfAnalysis, you gain a little stability under heavy load, but don't expect too much.
- Does not depend on any Android application layer framework or third-party kernel.
- Provides parameter-tuned configuration files for most popular hardware platforms.

## Supported SOCs at the moment

```plain
SDM Gen 1/SDM GEN 1+
SDM888/SDM888+
SDM865/SDM865+/SDM870
SDM855/SDM855+/SDM860
SDM845
SDM778G/SDM778G+
SDM765/SDM765G
SDM750G
SDM675/SDM730/SDM730G/SDM732
SDM710/SDM712
SDM662/SDM680/SDM685
SDM665 
SDM636/SDM660
```
## Download

https://github.com/WeirdMidas/uperf/releases

## Installation

### Magisk Method

1. After downloading, flash it via Magisk Manager. Magisk version must be at least 18.0.     
2. After rebooting, check `/sdcard/Android/yc/uperf/uperf_log.txt` to see if uperf starts automatically.

### Manual Installation

1. If your device cannot install Magisk and you have root access.       
2. Manually extract the downloaded file, for example, to `/data/uperf`.      
3. Modify the permissions of `setup_uperf.sh`, `run_uperf.sh`, and `initsvc_uperf.sh` to 755.     
4. Execute `setup_uperf.sh` to complete the installation and check the output for errors.      
5. Execute `run_uperf.sh` to start uperf and check the output for errors.      
6. Open `/data/cache/injector.log` and check if sfanalysis injection was successful.     
7. If associated with auto-starting a third-party app, set `run_uperf.sh` to execute after boot.     
8. If associated with auto-starting a system startup script, insert `sh /data/uperf/initsvc_uperf.sh`.     
9. After rebooting, check `/sdcard/yc/uperf/uperf_log.txt` to check if uperf starts normally.

### Performance Mode Switching

#### Modifying the Default Performance Mode at Startup

1. Open `/sdcard/Android/yc/uperf/cur_powermode.txt`.

2. Modify `auto`, where `auto` is the default performance mode used after boot. Available modes include:

- `auto` dynamically responds based on the app being used.   
- `balance` balanced mode, slightly smoother than the factory default while saving power.    
- `powersave` powersave mode, ensuring basic smoothness while minimizing power consumption.   
- `performance` performance mode, ensures that the scheduler is performance-oriented with an impact on energy consumption.    
- `fast` fast/benchmark mode, It seeks absolute performance based on the TDP of the device chassis.

3. Reboot

#### Switching performance modes after startup

Method 1: Execute `sh /data/powercfg.sh balance`, where `balance` is the name of the performance mode you want to switch to.

Method 2: Install [Scene](https://www.coolapk.com/apk/com.omarea.vtools) and bind the corresponding performance mode to the app.

## Frequently Asked Questions

Q: Does it negatively impact standby power consumption?    
A: Uperf's implementation incorporates numerous low-power optimizations, resulting in very low power consumption during operation. Furthermore, the pre-built configuration file's standby mode reduces the number of cores woken up during standby and uses conservative upsampling parameters. Standby power consumption optimization primarily relies on reducing the proportion of wake-up time; Uperf can further reduce standby power consumption on this basis.     

Q: Why is it still power-consuming even with Uperf?    
A: The power consumption of the SOC's AP portion mainly depends on the computational load and the frequency used. Uperf can only control performance release and improve frequency selection to reduce power consumption. If the background application's computational load is very high, it cannot significantly extend battery life. This issue can be located using the process manager in the Scene toolbox.    

Q: Should I disable system temperature control?     
A: System temperature control is a hardware protection measure or used to improve the user experience under high load. In most cases, it does not need to be disabled. If you encounter severely performance-limited situations, such as running competitive games where the CPU's maximum frequency is limited to 1.4GHz, please increase the temperature control intervention threshold or disable system temperature control.    

Q: What is the relationship between Uperf and the Scene Toolbox?      
A: These two software programs operate independently and do not depend on each other. Uperf implements interfaces that the Scene Toolbox can call, such as performance mode switching and app-specific performance modes. Performance mode switching can also be achieved without installing the Scene Toolbox; see the usage instructions for details.      

Q: Is it necessary to disable system performance boost?      
A: The scripts within the Uperf module have already disabled most mainstream user-mode and kernel-mode boosting. If there are any unconventional boosting attempts, the user needs to disable them manually.      

Q: I encountered some strange system malfunctions; what's going on?      
A: Uperf works normally on most platforms. During the testing phase, the following possible malfunctions were collected:    
- Desktop launcher not responding to touch. This is currently only encountered in MIUI 12. If you encounter this issue, please delete `/data/adb/modules/uperf/enable_sfanalysis`.     
- Intermittent loss of touchscreen reporting. Please check if you are using apps with automatic skip functionality, especially those that support coordinate clicks.     
- Sleeping/freezing. User-space applications should theoretically not affect system stability. Please replace with the official kernel and ROM.    

Q: Why does Magisk display a "not supported" error during installation?    
A: This hardware platform does not have a pre-built configuration file; you may need to adapt it yourself.

## Credits

Thanks to the following users or projects for their help with the source code of this project:     
- [@AndroidDumps](https://github.com/AndroidDumps)
- [TinyInjector](https://github.com/shunix/TinyInjector)
- [xHook](https://github.com/iqiyi/xHook)
- [@cjybyjk](https://github.com/cjybyjk)
- [@SatySatsZB](https://github.com/SatySatsZB)
- [@osm0sis](https://github.com/osm0sis)
- @YMJ

Thanks to the following users for their testing feedback and bug localization:     
- @HEX_Stan(coolapk)  
- @僞裝灬(coolapk)  
- @Yoooooo(coolapk)  
- @我愿你安i(coolapk)  
- @鹰雏(coolapk)  
- @yishisanren(coolapk)  
- @asd821385525(coolapk)  
- @倚楼醉听曲(coolapk)  
- @NepPoseidon(coolapk)  
- @寻光丿STLD(coolapk)  
- @比企谷の雪乃(coolapk)  
- @非洲咸鱼(coolapk)  
- @哔哩哔哩弹慕网(coolapk)  
- @我心飞翔的安(coolapk)  
- @浏泽仔(coolapk)  
- @〇MH1031(coolapk)  
- @今天我头条了吗(coolapk)  
- @瓜瓜皮(coolapk)  
- @Universes(coolapk)  
- @Superpinkcat(coolapk)  
- @asto18089(coolapk)  
- @顺其自然的肥肉(coolapk)  
- @酷斗吧(coolapk)  
- @何为永恒(coolapk)  
- @我为啥叫这个(coolapk)  
- @goddard(coolapk)  
- @正果sss(coolapk)  
- @Cowen(coolapk)  
- @瞬光飞翔(coolapk)  
- @kuiot(coolapk)  
- @常凯申将军(coolapk)  
- emptybot08(github)  
- ahzhi(github)  
- Saumer7(github)  