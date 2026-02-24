# #117 - New wordsize detection doesn't support powerpc64 or 64-bit RISC-V [Closed]

> Username: jwakely  
> Created at: 2021-02-01 11:26:40 UTC  
> Updated at: 2021-02-13 16:11:21 UTC  
> Closed at: 2021-02-13 16:11:21 UTC  
> Url: https://github.com/boostorg/predef/issues/117  

`predef/architecture/ppc.h` always defines wordsize to 32, which completely ignores the predominant powerpc systems in use today. You should check for `__powerpc64__`.  
  
It looks like `riscv.h` ignore 64-bit systems too. As far as I know, the Fedora and Debian ports for RISC-V only support the 64-bit variant, so assuming 32-bit seems of limited use.
