### Android 查看应用线程
```bash
# 1. 多个 adb 连接查看设备信息
adb devices
# 结果：
# 17f4f7f6        device
# e1ed6a68        device

# 2. 连接目标设备
adb -s e1ed6a68 shell

# 3. 查看应用进程
ps -ef|grep flutter_app
# 结果：
# u0_a576      10323   720 90 14:27:12 ?    00:00:04 com.example.flutter_app
# root         10379 10284 5 14:27:16 pts/0 00:00:00 grep flutter_app

# 4. 查看进程的线程
# 也可以使用
ps -T -p 10323
# 结果：
# USER           PID   TID  PPID     VSZ    RSS WCHAN            ADDR     FRZ  S CMD
# u0_a576      10323 10323   720 4500244 247092 SyS_epoll+          0     efg  S ple.flutter_app
# u0_a576      10323 10328   720 4500244 247092 futex_wai+          0     efg  S Jit thread pool
# u0_a576      10323 10329   720 4500244 247092 do_sigtim+          0     efg  S Signal Catcher
# u0_a576      10323 10330   720 4500244 247092 poll_sche+          0     efg  S ADB-JDWP Connec
# ...
```

### Android 查看应用内存
```bash
# 1. 多个 adb 连接查看设备信息
adb devices
# 结果：
# 17f4f7f6        device
# e1ed6a68        device

# 2. 连接目标设备
adb -s e1ed6a68 shell

# 3. 查看具体应用占用内存
dumpsys meminfo [在 AndroidManifest.xml 中配置的具体包名]
```

### adb push
Android push 包到指定设备: `adb -s e1ed6a68 push <local> <remote>`