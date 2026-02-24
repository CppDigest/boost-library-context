# #687 - Building a pure C program produces a binary with unneded dependencies [Open]

> Username: alxrmorozov  
> Created at: 2020-12-17 20:07:08 UTC  
> Updated at: 2021-05-29 16:23:04 UTC  
> Url: https://github.com/boostorg/build/issues/687  

a.c:  
```  
int main() { return 0; }  
```  
jamroot.jam:  
```  
exe a : a.c ;  
```  
Build:  
```  
$ b2 toolset=gcc -d+2  
common.mkdir bin  
  
        mkdir -p "bin"  
  
common.mkdir bin/gcc-8  
  
        mkdir -p "bin/gcc-8"  
  
common.mkdir bin/gcc-8/debug  
  
        mkdir -p "bin/gcc-8/debug"  
  
gcc.compile.c bin/gcc-8/debug/a.o  
  
    "g++" -x c -fPIC -O0 -fno-inline -Wall -g     -c -o "bin/gcc-8/debug/a.o" "a.c"  
  
gcc.link bin/gcc-8/debug/a  
  
    "g++"    -o "bin/gcc-8/debug/a" -Wl,--start-group "bin/gcc-8/debug/a.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -g  
  
```  
Resulting binary dependencies:  
```  
$ ldd bin/gcc-8/debug/a  
        linux-vdso.so.1 (0x00007ffd25cf3000)  
        libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f7373bab000)  
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f7373a28000)  
        libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f7373a0e000)  
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f737384d000)  
        /lib64/ld-linux-x86-64.so.2 (0x00007f7373d5e000)  
```  
If I build with gcc I get much less dependencies:  
```  
$ gcc a.c -o a  
$ ldd ./a  
        linux-vdso.so.1 (0x00007ffce34e5000)  
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f1774547000)  
        /lib64/ld-linux-x86-64.so.2 (0x00007f1774737000)  
```  
  
```  
$ b2 --version  
B2 4.2-git  
  
$ g++ --version  
g++ (Debian 8.3.0-6) 8.3.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
$ cat /etc/debian_version  
10.7  
$ arch  
x86_64  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:34 UTC  
> Url: https://github.com/boostorg/build/issues/687#issuecomment-850859626  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
