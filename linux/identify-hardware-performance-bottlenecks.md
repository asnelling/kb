---
description: >-
  Here's a collection of tools with usage examples to help locate resource
  bottlenecks, whether CPU, RAM, or I/O.
---

# Identify hardware performance bottlenecks

Credit: Inspired by Ronald Bradford: [https://ronaldbradford.com/blog/identifying-resource-bottlenecks-cpu-2009-03-31/](https://ronaldbradford.com/blog/identifying-resource-bottlenecks-cpu-2009-03-31/)

## CPU

### Gather information

For system processor details, run `lscpu` with no arguments.

```
$ lscpu
Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   39 bits physical, 48 bits virtual
CPU(s):                          4
On-line CPU(s) list:             0-3
Thread(s) per core:              2
Core(s) per socket:              2
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       GenuineIntel
CPU family:                      6
Model:                           61
Model name:                      Intel(R) Core(TM) i5-5300U CPU @ 2.30GHz
Stepping:                        4
CPU MHz:                         1529.140
CPU max MHz:                     2900.0000
CPU min MHz:                     500.0000
BogoMIPS:                        4589.26
Virtualization:                  VT-x
L1d cache:                       64 KiB
L1i cache:                       64 KiB
L2 cache:                        512 KiB
L3 cache:                        3 MiB
NUMA node0 CPU(s):               0-3
Vulnerability Itlb multihit:     KVM: Mitigation: Split huge pages
Vulnerability L1tf:              Mitigation; PTE Inversion; VMX conditional cache flushes, SMT vulnerable
Vulnerability Mds:               Mitigation; Clear CPU buffers; SMT vulnerable
Vulnerability Meltdown:          Mitigation; PTI
Vulnerability Spec store bypass: Mitigation; Speculative Store Bypass disabled via prctl and seccomp
Vulnerability Spectre v1:        Mitigation; usercopy/swapgs barriers and __user pointer sanitization
Vulnerability Spectre v2:        Mitigation; Full generic retpoline, IBPB conditional, IBRS_FW, STIBP conditional, RSB filling
Vulnerability Srbds:             Mitigation; Microcode
Vulnerability Tsx async abort:   Mitigation; Clear CPU buffers; SMT vulnerable
Flags:                           fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp 
                                 lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 sss
                                 e3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fau
                                 lt epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpc
                                 id rtm rdseed adx smap intel_pt xsaveopt dtherm ida arat pln pts md_clear flush_l1d
```

The `CPU(s)` row shows the number of logical cores seen by the OS. The number of physical cores, which may be less than the number of logical cores, is found by multiplying `Core(s) per socket` by `Socket(s)`.

If `lscpu` is not available:

```
$ cat /proc/cpuinfo
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 61
model name	: Intel(R) Core(TM) i5-5300U CPU @ 2.30GHz
stepping	: 4
microcode	: 0x2f
cpu MHz		: 1290.535
cache size	: 3072 KB
physical id	: 0
siblings	: 4
core id		: 0
cpu cores	: 2
apicid		: 0
initial apicid	: 0
fpu		: yes
fpu_exception	: yes
cpuid level	: 20
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb invpcid_single pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm rdseed adx smap intel_pt xsaveopt dtherm ida arat pln pts md_clear flush_l1d
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs taa itlb_multihit srbds
bogomips	: 4589.26
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:
...
```

### Identify a bottleneck

On an idle system, `vmstat` will look like this:

```
$ vmstat 1 10
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 3592880 1042360 3279096    0    0  1203  4108  348  510 26  4 62  9  0
 0  0      0 3592904 1042360 3279096    0    0     0     0   69  105  0  0 100  0  0
 0  0      0 3592872 1042360 3279096    0    0     0     0   76   84  0  0 100  0  0
 0  0      0 3592872 1042360 3279096    0    0     0     0   29   39  0  0 100  0  0
 0  0      0 3592872 1042360 3279096    0    0     0     0   34   46  0  0 100  0  0
 0  0      0 3592872 1042360 3279096    0    0     0     0   35   53  0  0 100  0  0
 0  0      0 3592872 1042360 3279096    0    0     0     0   34   51  0  0 100  0  0
 0  0      0 3592904 1042360 3279096    0    0     0     0   43   60  0  0 100  0  0
 0  0      0 3592904 1042360 3279096    0    0     0     0   32   40  0  0 100  0  0
 0  0      0 3592872 1042360 3279156    0    0    60     0   86  127  0  0 100  0  0
```

During CPU bottleneck it will instead look like this:

```
$ vmstat 1 10
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 9  1      0  146220   78152 6594520    0    0    16 139896 3556  675  93   7   0   0   0                                                                     
 8  0      0  264680   81952 6727932    0    0   376      0 2595 2380  94   5   1   0   0
 8  0      0  164400   84320 6846444    0    0   256   3800 2408 1393  94   6   0   0   0
10  0      0  283448   78232 6686196    0    0  3700      0 2114  343  95   5   0   0   0
10  1      0  211804   78216 6637856    0    0     0 153288 2731  552  95   5   0   0   0                                                                     
10  0      0  528524   85804 6574100    0    0  2328     36 4247  671  95   5   0   0   0                                                                      
 8  0      0  361120   78664 6724544    0    0  3832   1304 2173  408  96   3   0   0   0
10  1      0  326844   78200 6616304    0    0  1648 147188 2750  588  96   4   0   0   0                                                                     
10  0      0  308400   78232 6668804    0    0    96  68208 2340  281  96   4   0   0   0                                                                      
10  1      0  227820   78232 6656808    0    0    48 126692 2617  298  97   3   0   0   0
```

The noteworthy columns here are:

* **r:** The number of runnable processes (running or waiting for run time).
* **us:** Time spent running non-kernel code. (user time, including nice time)
* **sy:** Time spent running kernel code. (system time)
* **id:** Time spent idle.
* **wa:** Time spent waiting for IO.
* **st:** Time stolen from a virtual machine.

TODO: Add `mpstat` to the toolset.

## Memory

During normal workstation load, swap should see very little usage:

```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:          7.7Gi       4.9Gi       190Mi       1.7Gi       2.5Gi       715Mi
Swap:         4.0Gi       247Mi       3.8Gi
```

The system may release some (or all) `buff/cache` memory in response to increased demand, so the `free` column is slightly misleading.

To dive a little deeper:

```
$ cat /proc/meminfo 
MemTotal:        8038424 kB
MemFree:          222248 kB
MemAvailable:     599248 kB
Buffers:          107632 kB
Cached:          2078212 kB
SwapCached:         7432 kB
Active:          6117056 kB
Inactive:        1134832 kB
Active(anon):    5830400 kB
Inactive(anon):   872784 kB
Active(file):     286656 kB
Inactive(file):   262048 kB
Unevictable:      146004 kB
Mlocked:           18564 kB
SwapTotal:       4194300 kB
SwapFree:        3737596 kB
Dirty:               784 kB
Writeback:             0 kB
AnonPages:       5205284 kB
Mapped:           712736 kB
Shmem:           1628944 kB
KReclaimable:      94900 kB
Slab:             265740 kB
SReclaimable:      94900 kB
SUnreclaim:       170840 kB
KernelStack:       21008 kB
PageTables:        49932 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     8213512 kB
Committed_AS:   15986816 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       43912 kB
VmallocChunk:          0 kB
Percpu:             4896 kB
HardwareCorrupted:     0 kB
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
FileHugePages:         0 kB
FilePmdMapped:         0 kB
CmaTotal:              0 kB
CmaFree:               0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB
DirectMap4k:     1102752 kB
DirectMap2M:     7172096 kB
```

To identify which processes are using the most memory:

```
$ top -o '%MEM'
top - 23:34:27 up 1 day, 17 min,  1 user,  load average: 1.54, 1.27, 1.06
Tasks: 299 total,   1 running, 298 sleeping,   0 stopped,   0 zombie
%Cpu(s): 24.8 us,  5.8 sy,  0.0 ni, 68.7 id,  0.3 wa,  0.0 hi,  0.4 si,  0.0 st
MiB Mem :   7850.0 total,    151.6 free,   5498.2 used,   2200.2 buff/cache
MiB Swap:   4096.0 total,   3650.5 free,    445.5 used.    489.4 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                                                                     
   2796 asnell    20   0 7282856   1.1g 260892 S  33.2  14.8 130:58.38 GeckoMain                                                                                                   
  62041 asnell    20   0 3859896   1.0g 178196 S   1.0  13.6  20:53.83 Web Content                                                                                                 
  52322 asnell    20   0 3396028 729608  69660 S  53.8   9.1  18:09.88 Web Content                                                                                                 
   2248 asnell    20   0 5354612 346688 102180 S  13.6   4.3  54:46.25 gnome-shell                                                                                                 
  51751 asnell    20   0   46.5g 324204  54724 S   0.0   4.0  30:12.35 codium                                                                                                      
   3352 asnell    20   0  853164 283048 144668 S   0.0   3.5  36:41.92 codium                                                                                                      
  62189 asnell    20   0 2683184 229016  56772 S   1.0   2.8   1:24.59 Web Content                                                                                                 
  51828 asnell    20   0  453740 219780   3820 S   0.0   2.7   3:02.79 python                                                                                                      
  52288 asnell    20   0 3263692 217180  70376 S   0.0   2.7   5:35.48 Web Content                                                                                                 
   4603 asnell    20   0 2709904 194900  57956 S   0.3   2.4   7:53.07 Web Content                                                                                                 
  54010 asnell    20   0 2924876 179732  52792 S   0.3   2.2   2:16.05 Web Content                                                                                                 
  62069 asnell    20   0 2573152 164788  54268 S   0.3   2.1   2:20.39 Web Content                                                                                                 
   2971 asnell    20   0 8917472 158312  51372 S   0.3   2.0   6:19.58 WebExtensions                                                                                               
  51771 asnell    20   0   36.3g 137792  15092 S   0.0   1.7   2:00.63 codium                                                                                                      
   2095 asnell    20   0 1104500 137312  30552 S  11.3   1.7  84:47.81 Xorg                                                                                                        
 103575 asnell    20   0 2541548 135656  59824 S   0.3   1.7   1:03.74 Web Content                                                                                                 
   2931 asnell    20   0 2495640 135604  51480 S   0.3   1.7   1:36.45 Privileged Cont                                                                                             
   3319 asnell    20   0   36.8g  99180  32156 S   0.3   1.2   8:29.27 codium                                                                                                      
   3423 asnell    20   0   36.3g  53724  17244 S   0.0   0.7   1:01.66 codium                                                                                                      
   1058 root      20   0  996852  53560  10708 S   0.0   0.7   1:38.38 multipassd                                                                                                  
  55335 asnell    20   0   36.2g  51356  10176 S   0.0   0.6   0:17.13 codium                                                                                                      
   2509 asnell    20   0  711984  51028   2564 S   0.0   0.6   2:23.73 multipass.gui                                                                                               
   3449 asnell    20   0   36.3g  47976  11452 S   0.0   0.6   2:41.70 codium
```

The `-o '%MEM'` option sorts processes by physical memory utilization, the greatest offenders first.
