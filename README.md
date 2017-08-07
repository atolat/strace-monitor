# strace-monitor
Utility to monitor application code stack traces for performance optimization.

Every application code maps to the OS user space and makes system calls. We can gather a ton of information from application code traces, and figure out how exactly an application's code interacts with the OS. A simple example is the trace of a "Hello World" in C and shell.

Shell
```
arpan@arpan-HP:~$ cat hello.sh
#! /bin/bash
echo "Hello World"
arpan@arpan-HP:~$ strace -ttT ./hello.sh
23:10:48.120781 execve("./hello.sh", ["./hello.sh"], [/* 57 vars */]) = 0 <0.000473>
23:10:48.121722 brk(NULL)               = 0x21e2000 <0.000083>
23:10:48.121915 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000054>
23:10:48.122053 mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fe1f96e7000 <0.000036>
23:10:48.122186 access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory) <0.000031>
23:10:48.122303 open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3 <0.000035>
23:10:48.122401 fstat(3, {st_mode=S_IFREG|0644, st_size=135222, ...}) = 0 <0.000026>
23:10:48.122510 mmap(NULL, 135222, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fe1f96c5000 <0.000030>
23:10:48.122602 close(3)                = 0 <0.000023>
23:10:48.122690 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000028>
23:10:48.122789 open("/lib/x86_64-linux-gnu/libtinfo.so.5", O_RDONLY|O_CLOEXEC) = 3 <0.000033>
23:10:48.122880 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\360\306\0\0\0\0\0\0"..., 832) = 832 <0.000028>
23:10:48.122975 fstat(3, {st_mode=S_IFREG|0644, st_size=166680, ...}) = 0 <0.000024>
23:10:48.123068 mmap(NULL, 2263840, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fe1f929c000 <0.000029>
23:10:48.123157 mprotect(0x7fe1f92c1000, 2093056, PROT_NONE) = 0 <0.000035>
23:10:48.123248 mmap(0x7fe1f94c0000, 20480, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x24000) = 0x7fe1f94c0000 <0.000035>
23:10:48.123360 close(3)                = 0 <0.000022>
23:10:48.123441 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000027>
23:10:48.123531 open("/lib/x86_64-linux-gnu/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3 <0.000032>
23:10:48.123622 read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\200\r\0\0\0\0\0\0"..., 832) = 832 <0.000026>
23:10:48.123714 fstat(3, {st_mode=S_IFREG|0644, st_size=14608, ...}) = 0 <0.000024>
23:10:48.123805 mmap(NULL, 2109680, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fe1f9098000 <0.000030>
23:10:48.123896 mprotect(0x7fe1f909b000, 2093056, PROT_NONE) = 0 <0.000033>
23:10:48.123986 mmap(0x7fe1f929a000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x2000) = 0x7fe1f929a000 <0.000033>
23:10:48.124095 close(3)                = 0 <0.000023>
23:10:48.124173 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000033>
23:10:48.124248 open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3 <0.000017>
23:10:48.124297 read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20\5\2\0\0\0\0\0"..., 832) = 832 <0.000014>
23:10:48.124347 fstat(3, {st_mode=S_IFREG|0755, st_size=1856752, ...}) = 0 <0.000012>
23:10:48.124397 mmap(NULL, 3959200, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fe1f8cd1000 <0.000016>
23:10:48.124443 mprotect(0x7fe1f8e8f000, 2093056, PROT_NONE) = 0 <0.000021>
23:10:48.124493 mmap(0x7fe1f908e000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1bd000) = 0x7fe1f908e000 <0.000018>
23:10:48.124548 mmap(0x7fe1f9094000, 14752, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fe1f9094000 <0.000015>
23:10:48.124599 close(3)                = 0 <0.000012>
23:10:48.124657 mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fe1f96c3000 <0.000015>
23:10:48.124705 arch_prctl(ARCH_SET_FS, 0x7fe1f96c3b40) = 0 <0.000013>
23:10:48.124806 mprotect(0x7fe1f908e000, 16384, PROT_READ) = 0 <0.000018>
23:10:48.124857 mprotect(0x7fe1f929a000, 4096, PROT_READ) = 0 <0.000066>
23:10:48.125000 mprotect(0x7fe1f94c0000, 16384, PROT_READ) = 0 <0.000016>
23:10:48.125055 mprotect(0x700000, 12288, PROT_READ) = 0 <0.000015>
23:10:48.125100 mprotect(0x7fe1f96ea000, 4096, PROT_READ) = 0 <0.000016>
23:10:48.125146 munmap(0x7fe1f96c5000, 135222) = 0 <0.000024>
23:10:48.125240 open("/dev/tty", O_RDWR|O_NONBLOCK) = 3 <0.000023>
23:10:48.125292 close(3)                = 0 <0.000015>
23:10:48.125362 brk(NULL)               = 0x21e2000 <0.000012>
23:10:48.125399 brk(0x21e3000)          = 0x21e3000 <0.000015>
23:10:48.125448 open("/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3 <0.000018>
23:10:48.125500 fstat(3, {st_mode=S_IFREG|0644, st_size=2993552, ...}) = 0 <0.000014>
23:10:48.125550 mmap(NULL, 2993552, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fe1f89f6000 <0.000017>
23:10:48.125603 close(3)                = 0 <0.000012>
23:10:48.125650 brk(0x21e4000)          = 0x21e4000 <0.000015>
23:10:48.125701 brk(0x21e5000)          = 0x21e5000 <0.000013>
23:10:48.125777 getuid()                = 1000 <0.000012>
23:10:48.125816 getgid()                = 1000 <0.000012>
23:10:48.125857 geteuid()               = 1000 <0.000012>
23:10:48.125897 getegid()               = 1000 <0.000008>
23:10:48.125940 rt_sigprocmask(SIG_BLOCK, NULL, [], 8) = 0 <0.000008>
23:10:48.125974 ioctl(-1, TIOCGPGRP, 0x7fffbbeddd1c) = -1 EBADF (Bad file descriptor) <0.000008>
23:10:48.126005 brk(0x21e6000)          = 0x21e6000 <0.000008>
23:10:48.126036 sysinfo({uptime=539341, loads=[21888, 25696, 27744], totalram=16736706560, freeram=5302403072, sharedram=3057070080, bufferram=218947584, totalswap=17090736128, freeswap=17090736128, procs=891, totalhigh=0, freehigh=0, mem_unit=1}) = 0 <0.000010>
23:10:48.126067 brk(0x21e7000)          = 0x21e7000 <0.000008>
23:10:48.126118 rt_sigaction(SIGCHLD, {SIG_DFL, [], SA_RESTORER|SA_RESTART, 0x7fe1f8d067f0}, {SIG_DFL, [], 0}, 8) = 0 <0.000008>
23:10:48.126149 rt_sigaction(SIGCHLD, {SIG_DFL, [], SA_RESTORER|SA_RESTART, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER|SA_RESTART, 0x7fe1f8d067f0}, 8) = 0 <0.000008>
23:10:48.126181 rt_sigaction(SIGINT, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], 0}, 8) = 0 <0.000008>
23:10:48.126213 rt_sigaction(SIGINT, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, 8) = 0 <0.000008>
23:10:48.126244 rt_sigaction(SIGQUIT, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], 0}, 8) = 0 <0.000008>
23:10:48.126275 rt_sigaction(SIGQUIT, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, 8) = 0 <0.000008>
23:10:48.126307 rt_sigaction(SIGTSTP, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], 0}, 8) = 0 <0.000009>
23:10:48.126339 rt_sigaction(SIGTSTP, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, 8) = 0 <0.000008>
23:10:48.126370 rt_sigaction(SIGTTIN, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], 0}, 8) = 0 <0.000008>
23:10:48.126400 rt_sigaction(SIGTTIN, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, 8) = 0 <0.000008>
23:10:48.126432 rt_sigaction(SIGTTOU, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], 0}, 8) = 0 <0.000008>
23:10:48.126462 rt_sigaction(SIGTTOU, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, 8) = 0 <0.000007>
23:10:48.126495 rt_sigprocmask(SIG_BLOCK, NULL, [], 8) = 0 <0.000008>
23:10:48.126525 rt_sigaction(SIGQUIT, {SIG_IGN, [], SA_RESTORER, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER, 0x7fe1f8d067f0}, 8) = 0 <0.000007>
23:10:48.126557 uname({sysname="Linux", nodename="arpan-HP", ...}) = 0 <0.000008>
23:10:48.126592 brk(0x21eb000)          = 0x21eb000 <0.000009>
23:10:48.126623 brk(0x21ed000)          = 0x21ed000 <0.000008>
23:10:48.126651 brk(0x21ef000)          = 0x21ef000 <0.000008>
23:10:48.126681 brk(0x21f0000)          = 0x21f0000 <0.000009>
23:10:48.126727 brk(0x21f1000)          = 0x21f1000 <0.000008>
23:10:48.126760 brk(0x21f2000)          = 0x21f2000 <0.000008>
23:10:48.126790 brk(0x21f3000)          = 0x21f3000 <0.000008>
23:10:48.126819 stat("/home/arpan", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0 <0.000011>
23:10:48.126850 stat(".", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0 <0.000009>
23:10:48.126885 stat("/home", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0 <0.000009>
23:10:48.126917 stat("/home/arpan", {st_mode=S_IFDIR|0755, st_size=4096, ...}) = 0 <0.000008>
23:10:48.126951 getpid()                = 24411 <0.000007>
23:10:48.126992 open("/usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY) = 3 <0.000011>
23:10:48.127018 fstat(3, {st_mode=S_IFREG|0644, st_size=26258, ...}) = 0 <0.000008>
23:10:48.127045 mmap(NULL, 26258, PROT_READ, MAP_SHARED, 3, 0) = 0x7fe1f96e0000 <0.000009>
23:10:48.127071 close(3)                = 0 <0.000007>
23:10:48.127102 getppid()               = 24409 <0.000006>
23:10:48.127137 brk(0x21f4000)          = 0x21f4000 <0.000007>
23:10:48.127165 brk(0x21f5000)          = 0x21f5000 <0.000007>
23:10:48.127190 brk(0x21f6000)          = 0x21f6000 <0.000007>
23:10:48.127214 getpgrp()               = 24409 <0.000007>
23:10:48.127235 ioctl(2, TIOCGPGRP, [24409]) = 0 <0.000007>
23:10:48.127261 rt_sigaction(SIGCHLD, {0x44cf60, [], SA_RESTORER|SA_RESTART, 0x7fe1f8d067f0}, {SIG_DFL, [], SA_RESTORER|SA_RESTART, 0x7fe1f8d067f0}, 8) = 0 <0.000007>
23:10:48.127290 getrlimit(RLIMIT_NPROC, {rlim_cur=63484, rlim_max=63484}) = 0 <0.000007>
23:10:48.127324 brk(0x21f7000)          = 0x21f7000 <0.000007>
23:10:48.127358 brk(0x21f8000)          = 0x21f8000 <0.000007>
23:10:48.127417 rt_sigprocmask(SIG_BLOCK, NULL, [], 8) = 0 <0.000007>
23:10:48.127442 brk(0x21f9000)          = 0x21f9000 <0.000008>
23:10:48.127466 open("./hello.sh", O_RDONLY) = 3 <0.000011>
23:10:48.127496 stat("./hello.sh", {st_mode=S_IFREG|0755, st_size=32, ...}) = 0 <0.000008>
23:10:48.127528 ioctl(3, TCGETS, 0x7fffbbeddcb0) = -1 ENOTTY (Inappropriate ioctl for device) <0.000007>
23:10:48.127554 lseek(3, 0, SEEK_CUR)   = 0 <0.000007>
23:10:48.127578 read(3, "#! /bin/bash\necho \"Hello World\"\n", 80) = 32 <0.000008>
23:10:48.127605 lseek(3, 0, SEEK_SET)   = 0 <0.000006>
23:10:48.127629 getrlimit(RLIMIT_NOFILE, {rlim_cur=1024, rlim_max=4*1024}) = 0 <0.000007>
23:10:48.127654 fcntl(255, F_GETFD)     = -1 EBADF (Bad file descriptor) <0.000007>
23:10:48.127679 dup2(3, 255)            = 255 <0.000008>
23:10:48.127702 close(3)                = 0 <0.000007>
23:10:48.127725 fcntl(255, F_SETFD, FD_CLOEXEC) = 0 <0.000007>
23:10:48.127747 fcntl(255, F_GETFL)     = 0x8000 (flags O_RDONLY|O_LARGEFILE) <0.000007>
23:10:48.127771 fstat(255, {st_mode=S_IFREG|0755, st_size=32, ...}) = 0 <0.000007>
23:10:48.127797 lseek(255, 0, SEEK_CUR) = 0 <0.000007>
23:10:48.127819 brk(0x21fa000)          = 0x21fa000 <0.000007>
23:10:48.127847 read(255, "#! /bin/bash\necho \"Hello World\"\n", 32) = 32 <0.000007>
23:10:48.127894 write(1, "Hello World\n", 12Hello World
) = 12 <0.000010>
23:10:48.127928 brk(0x21fb000)          = 0x21fb000 <0.000008>
23:10:48.127957 read(255, "", 32)       = 0 <0.000008>
23:10:48.127983 rt_sigprocmask(SIG_BLOCK, [CHLD], [], 8) = 0 <0.000007>
23:10:48.128009 rt_sigprocmask(SIG_SETMASK, [], NULL, 8) = 0 <0.000006>
23:10:48.128046 exit_group(0)           = ?
23:10:48.128126 +++ exited with 0 +++

```
C
```
arpan@arpan-HP:~$ cat hello.c
#include <stdio.h>

int main(){

printf("Hello World");
return 0;


}
arpan@arpan-HP:~$ strace -ttT ./hello
23:11:56.639587 execve("./hello", ["./hello"], [/* 57 vars */]) = 0 <0.000841>
23:11:56.641024 brk(NULL)               = 0x564bc535b000 <0.000095>
23:11:56.641268 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000066>
23:11:56.641454 mmap(NULL, 12288, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5db193c000 <0.000079>
23:11:56.641702 access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory) <0.000041>
23:11:56.641828 open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3 <0.000037>
23:11:56.641930 fstat(3, {st_mode=S_IFREG|0644, st_size=135222, ...}) = 0 <0.000023>
23:11:56.642031 mmap(NULL, 135222, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f5db191a000 <0.000031>
23:11:56.642122 close(3)                = 0 <0.000022>
23:11:56.642209 access("/etc/ld.so.nohwcap", F_OK) = -1 ENOENT (No such file or directory) <0.000027>
23:11:56.642319 open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3 <0.000026>
23:11:56.642391 read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20\5\2\0\0\0\0\0"..., 832) = 832 <0.000024>
23:11:56.642464 fstat(3, {st_mode=S_IFREG|0755, st_size=1856752, ...}) = 0 <0.000033>
23:11:56.642552 mmap(NULL, 3959200, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f5db1353000 <0.000027>
23:11:56.642619 mprotect(0x7f5db1511000, 2093056, PROT_NONE) = 0 <0.000033>
23:11:56.642688 mmap(0x7f5db1710000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1bd000) = 0x7f5db1710000 <0.000033>
23:11:56.642772 mmap(0x7f5db1716000, 14752, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f5db1716000 <0.000024>
23:11:56.642853 close(3)                = 0 <0.000017>
23:11:56.642930 mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f5db1918000 <0.000020>
23:11:56.642995 arch_prctl(ARCH_SET_FS, 0x7f5db1918700) = 0 <0.000017>
23:11:56.643146 mprotect(0x7f5db1710000, 16384, PROT_READ) = 0 <0.000025>
23:11:56.643212 mprotect(0x564bc4204000, 4096, PROT_READ) = 0 <0.000021>
23:11:56.643272 mprotect(0x7f5db193f000, 4096, PROT_READ) = 0 <0.000023>
23:11:56.643330 munmap(0x7f5db191a000, 135222) = 0 <0.000037>
23:11:56.643464 fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 2), ...}) = 0 <0.000022>
23:11:56.643632 brk(NULL)               = 0x564bc535b000 <0.000019>
23:11:56.643689 brk(0x564bc537c000)     = 0x564bc537c000 <0.000021>
23:11:56.643770 write(1, "Hello World", 11Hello World) = 11 <0.000030>
23:11:56.643850 exit_group(0)           = ?
23:11:56.644015 +++ exited with 0 +++
```

A quick look at the traces indicates a precompiled C program is much lighter on the OS compared to interpreted code, shell(seems pretty obvious, right!).



The first task is to figure out how to gather and ship these stack traces to an analytics engine/stack (ELK maybe...) and then as we learn more about the OS and system calls, we should be able to make more sense of the stack traces!


Resources/Links
>Strace
http://www.brendangregg.com/blog/2014-05-11/strace-wow-much-syscall.html
http://chadfowler.com/2014/01/26/the-magic-of-strace.html

>OS
http://pages.cs.wisc.edu/~remzi/OSTEP/

