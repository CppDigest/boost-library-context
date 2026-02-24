# #123 Fix ppc32 on Linux musl, NetBSD, OpenBSD; fixes #120 [Merged]

> Username: kernigh  
> Created at: 2019-10-19 21:57:23 UTC  
> Updated at: 2019-10-24 02:59:15 UTC  
> Merged at: 2019-10-23 09:19:55 UTC  
> Closed at: 2019-10-23 09:19:55 UTC  
> Url: https://github.com/boostorg/context/pull/123  

This fixes fcontext on my PowerBook G4 running Void Linux  
ppc-musl-20190901, NetBSD/macppc 8.1, or OpenBSD/macppc 6.6-current,  
all with g++.  These systems use fcontext for *ppc32_sysv_elf*  
(PowerPC 32-bit System V ELF).  The assembly code was wrong for BSD  
and crashing on Linux musl.  
  
Linux returns a transfer_t in memory (through a hidden pointer in R3),  
but other systems (at least NetBSD and OpenBSD) return a transfer_t in  
registers R3:R4.  jump_fcontext() and ontop_fcontext() were always  
using the hidden pointer.  Add checks for `#ifdef__linux__`; start  
using R3:R4 on other systems.  
  
make_fcontext() was calling _exit(0) through the insecure BSS PLT.  
Set R30 to use the secure PLT.  This prevents a crash when musl's  
ld.so loads the executable; musl seems to require the secure PLT.  
  
Fix ontop_fcontext() to restore the hidden pointer on Linux.  It was  
passing the wrong context's hidden pointer to the ontop-function fn(),  
so fn() returned a transfer_t to the wrong stack.  When fn() was  
context_exit() in <boost/context/continuation_fcontext.hpp>, it freed  
the old stack, then returned `transfer_t{ nullptr, nullptr }` to free  
memory.  This crashed on Linux musl.  
  
Now that ontop_fcontext() restores the hidden pointer, it must stop  
abusing the same pointer to pass a transfer_t argument to fn().  Add a  
new ontop_fcontext_tail() in C++, which takes arguments in registers  
and allocates a transfer_t.  The code is in C++ so it can free the  
transfer_t argument if fn() throws a C++ exception.  
  
Rearrange the context frame to shrink it from 244 to 240 bytes.  This  
fixes the stack alignment: the ABI requires R1 % 16 == 0, and  
make_fcontext() respects this, but jump_fcontext() was adding 244 to  
R1, so the new context ran with a misaligned stack (244 % 16 == 4).  
  
Remove R13 from the context frame, so new contexts stop loading R13  
with garbage.  The ABI uses R13 to point to the executable's small  
data, so R13 should have the same value in every context.  
  
Add the backchain to the context frame; make room by moving LR to the  
caller's frame.  Order CR, R14 to R31, F14 to F31 at the frame's end,  
as is typical for this ABI.  Provide 8-byte alignment for FPSCR and  
F14 to F31, to avoid a misalignment penalty.

---

## Comment 1

> Username: olk  
> Created_at: 2019-10-23 09:19:47 UTC  
> Url: https://github.com/boostorg/context/pull/123#issuecomment-545352149  

I'm wondering why Linux uses a hidden pointer for transfer_t while xBSD use two registers for both members - does Linux and xBSD use different calling conventions?

---

## Comment 2

> Username: kernigh  
> Created_at: 2019-10-23 18:43:48 UTC  
> Updated_at: 2019-10-24 02:59:15 UTC  
> Url: https://github.com/boostorg/context/pull/123#issuecomment-545582480  

The conventions of Linux and BSD (on PowerPC 32-bit) are almost exactly the same. They differ when a function returns a small struct (or union) of up to 8 bytes. This affects Boost context, because transfer_t is an 8-byte struct.  
  
I didn't know this difference until February 2019, when I reported [LLVM bug #40736](https://bugs.llvm.org/show_bug.cgi?id=40736). LLVM and clang now use Linux's convention, so they are incompatible with GCC on BSD.  
  
I don't know why Linux and BSD differ, but it might be an accident of GCC. The earliest PowerPC ports of mkLinux and NetBSD used GCC. The oldest binaries from GCC might have returned small structs through hidden pointers. Then GCC might have switched to returning small structs in registers R3:R4, except on Linux, to stay compatible with old Linux binaries.  
  
Linux and BSD use ELF objects, while IBM's AIX uses XCOFF objects for PowerPC code. GCC might have targeted XCOFF before ELF. You can't put XCOFF relocations in an ELF file, so GCC needed the ELF relocations from the [_System V ABI, PowerPC_](https://refspecs.linuxbase.org/elf/elfspec_ppc.pdf). Sun wrote this ELF ABI, perhaps while trying to port Solaris to PowerPC. _(Edit: not "port Solaris to Linux.")_ It defines the ELF relocations and the calling conventions for functions.  
  
ELF and [XCOFF calling conventions](https://www.ibm.com/support/knowledgecenter/en/ssw_aix_72/assembler/idalangref_reg_use_conv.html) are similar but different. GCC would have needed changes to use the ELF conventions. One of the changes should be to return small structs in R3:R4 (_System V ABI_, "Return Values", page 3-32), but GCC might have missed this change. XCOFF seems to to return small structs in memory through a hidden pointer. The [gcc(1) manual](http://man7.org/linux/man-pages/man1/gcc.1.html) documents these options:  
  
```  
       -maix-struct-return  
           Return all structures in memory (as specified by the AIX ABI).  
  
       -msvr4-struct-return  
           Return structures smaller than 8 bytes in registers (as specified  
           by the SVR4 ABI).  
```  
  
I suspect that Linux defaults to `gcc -maix-struct-return`, and BSD defaults to `gcc -msvr4-struct-return`.

---

## Comment 3

> Username: olk  
> Created_at: 2019-10-23 20:15:15 UTC  
> Url: https://github.com/boostorg/context/pull/123#issuecomment-545616959  

ty

---
