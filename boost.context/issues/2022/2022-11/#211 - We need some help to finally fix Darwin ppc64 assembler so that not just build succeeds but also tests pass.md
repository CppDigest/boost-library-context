# #211 - We need some help to finally fix Darwin ppc64 assembler so that not just build succeeds but also tests pass [Open]

> Username: barracuda156  
> Created at: 2022-11-24 06:51:03 UTC  
> Updated at: 2026-02-01 23:39:37 UTC  
> Url: https://github.com/boostorg/context/issues/211  

In a couple of recent PRs I have made some fixes to Darwin PPC assembler here (nothing big, just fixing which was actually breaking the build), which fixed _building_ of `context` and made it _somewhat_ functional (in a sense that certain dependencies that require `context` now build successfully, `folly` and friends being the case).  
However, tests do not pass: https://github.com/macports/macports-ports/pull/16407#issuecomment-1304974991  
  
From earlier PRs by @kernigh and @DaoWen for PPC ELF it looks like the issue is quite non-trivial and requires a thorough understanding of both what Boost is doing and the _magic code_ :)  
Unfortunately, my knowledge of PPC assembler is rather limited and does not suffice here. I am not going to drop the case, since we really want this fixed properly, but any advice or help will be greatly appreciated.  
  
_This has been broken apparently forever – can we fix it now?_ :)  
  
(Considering a recent case with Boost shared pointer (https://github.com/boostorg/smart_ptr/issues/105), I cannot be entirely sure that it is assembler which is to blame and not again some wrong alignment, but probably it is assembler, given that it was also broken for *BSD.)  
  
P. S. For the record, alternative version of PPC code for `context` from the following repo also fails, at least with Fibonacci test: https://github.com/twlostow/libcontext  
  
P. P. S. @iains I am aware you are very busy atm, so please feel free to completely ignore the issue. But still tagging you as the person who understands Darwin magic code best.

---

## Comment 1

> Username: olk  
> Created at: 2022-11-25 06:24:20 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1327067188  

Seams to be a NULL pointer is dereferenced - this might happen because of the calling convention is violated.  
I don't own a PPC64(?) system and I'm too busy atm.  
You could debug a simple test app (simple jump) by stepping through the assembler instructions and compare the content in teh registers with what the calling convention requires.

---

## Comment 2

> Username: kernigh  
> Created at: 2022-11-27 02:16:24 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1328154669  

My PPC32 hardware is running OpenBSD, but I have a QEMU running Darwin. I can't compile Boost for Darwin, because my gcc 3.3 can't do C++11, but I can compile *ppc32_sysv_macho*.S and call them from C.  
  
I found a problem: `make_fcontext(_, _, there)` fails to pass the transfer_t there. It is passing r3 = address of transfer_t, but there wants r3:r4 = transfer_t. You can jump there, but there can't jump to its transfer_t's fctx; jump_fcontext will crash.  
  
This diff seems to fix make_context, but ontop_context might still be broken.  
  
```  
diff --git a/make_ppc32_sysv_macho_gas.S b/make_ppc32_sysv_macho_gas.S  
--- a/make_ppc32_sysv_macho_gas.S  
+++ b/make_ppc32_sysv_macho_gas.S  
@@ -85,11 +85,12 @@  
     clrrwi  r3, r3, 4  
   
     ; reserve space for context-data on context-stack  
-    ; including 64 byte of linkage + parameter area (R1  16 == 0)  
+    ; including 64 byte of linkage + parameter area (R1 % 16 == 0)  
     subi  r3, r3, 336  
   
     ; third arg of make_fcontext() == address of context-function  
-    stw  r5, 240(r3)  
+    ; store as trampoline's R31  
+    stw  r5, 224(r3)  
   
     ; set back-chain to zero  
     li   r0, 0  
@@ -106,12 +107,15 @@  
     ; load LR  
     mflr  r0  
     ; jump to label 1  
-    bl  l1  
-l1:  
+    bcl  20, 31, L1  
+L1:  
     ; load LR into R4  
     mflr  r4  
+    ; compute abs address of trampoline, use as PC  
+    addi  r5, r4, lo16(Ltrampoline - L1)  
+    stw  r5, 240(r3)  
     ; compute abs address of label finish  
-    addi  r4, r4, lo16((finish - .) + 4)  
+    addi  r4, r4, lo16(Lfinish - L1)  
     ; restore LR  
     mtlr  r0  
     ; save address of finish as return-address for context-function  
@@ -123,15 +127,28 @@ l1:  
   
     blr  ; return pointer to context-data  
   
-finish:  
-    ; save return address into R0  
-    mflr  r0  
-    ; save return address on stack, set up stack frame  
-    stw  r0, 4(r1)  
-    ; allocate stack space, R1  16 == 0  
-    stwu  r1, -16(r1)  
+Ltrampoline:  
+    ; We get R31 = context-function, R3 = address of transfer_t,  
+    ; but we need to pass R3:R4 = transfer_t.  
+    mtctr  r31  
+    lwz  r4, 4(r3)  
+    lwz  r3, 0(r3)  
+    bctr  
   
+Lfinish:  
+    ; load address of _exit into CTR  
+    bcl  20, 31, L2  
+L2:  
+    mflr  r4  
+    addis  r4, r4, ha16(Lexitp - L2)  
+    lwz  r4, lo16(Lexitp - L2)(r4)  
+    mtctr  r4  
     ; exit code is zero  
     li  r3, 0  
     ; exit application  
-    bl  _exit  
+    bctr  
+  
+.const_data  
+.align 2  
+Lexitp:  
+    .long  __exit  
```  
  
I put a few other changes in the diff.  
  
- I used L to hide local labels from gdb.  
- I used `bcl 20, 31, L1` as a branch hint, like gcc; see _MPC7450 RISC Microprocessor Family Reference Manual_ (MPC7450UM.pdf), 6.7.1.4.2 Position-Independent Code Example.  
- I added Lexitp, because my linker rejected `bl _exit`, "ld: /var/tmp//ccBVLXX8.o has external relocation entries in non-writable section (__TEXT,__text) for symbols: _exit". If the C++ compiler comes with a better linker, you might not need this change; but you should change `_exit` to `__exit`, which changes the call from exit(0) to _exit(0), to be like the other platforms.  
  
Someone else, who has the C++ compiler, can check the diff, edit it, and git commit it.  
  
My diff doesn't fix an alignment problem. make_fcontext sets the correct alignment 16, but jump_fcontext does addi r1, r1, 244, and 244 isn't a multiple of 16. When you jump to the new context, you will have a misaligned stack pointer. This will break altivec code (but PPC32 compilers tend to disable altivec) and slow down code that has 8-byte floats on the stack. I fixed the alignment for ELF in [df8fb6b](https://github.com/boostorg/context/commit/df8fb6b5289492914dd2af479e40e9737399fd21) by rearranging the frame from 244 to 240 bytes. A similar fix might help Mach-o, but ELF and Mach-o have different stack layouts, so you can't copy ELF's layout.

---

## Comment 3

> Username: barracuda156  
> Created at: 2022-11-27 06:16:13 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1328179845  

@kernigh Thank you very much! I will try building it now and running tests.  
  
For the stack, I think we can make it 240, since now it is saving “hidden”, which was copied from ELF version or wherever. In fact an earlier `libcontext` version did not have it, and used 240.  
  
P. S. If you can run at least Tiger in QEMU, @catap has fixed GCC10 for it.

---

## Comment 4

> Username: iains  
> Created at: 2022-11-27 11:22:20 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1328225108  

I cannot comment on much here (no time to load context myself, so not sure exactly what frame you are discussing) but ...  
  
> My diff doesn't fix an alignment problem. make_fcontext sets the correct alignment 16, but jump_fcontext does addi r1, r1, 244, and 244 isn't a multiple of 16. When you jump to the new context, you will have a misaligned stack pointer. This will break altivec code (but PPC32 compilers tend to disable altivec) and slow down code that has 8-byte floats on the stack. I fixed the alignment for ELF in [df8fb6b](https://github.com/boostorg/context/commit/df8fb6b5289492914dd2af479e40e9737399fd21) by rearranging the frame from 244 to 240 bytes. A similar fix might help Mach-o, but ELF and Mach-o have different stack layouts, so you can't copy ELF's layout.  
  
Darwin ppc32 ABI includes altivec, it cannot be disabled (although 10.4 will run on a G3, it does so by specific code that tests if altivec is present and skips the save/restore - it does not remove the space from the ABI).  
  
So this needs fixing - we know what the stack layout of a saved context should be ... if the synthesised one does not preserve alignment, it is probably still not quite right.

---

## Comment 5

> Username: kernigh  
> Created at: 2022-11-27 22:04:54 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1328350777  

@barracuda156 I believe that we need the "hidden" pointer to return a transfer_t, as `transfer_t jump_fcontext(void * from, void (* fn)(transfer_t))` is really `void jump_fcontext(transfer_t * hidden, void * from, void (* fn)(transfer_t))` in Darwin's ABI. Commit ba357205 had added the hidden pointer when it changed jump_fcontext's return type from void * to transfer_t.  
  
The stack layout is in the [32-bit PowerPC Function Calling Conventions](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/LowLevelABI/100-32-bit_PowerPC_Function_Calling_Conventions/32bitPowerPC.html#//apple_ref/doc/uid/TP40002438-SW20) of the old _OS X ABI Function Call Guide_ in Apple's documentation archive. The calling routine reserves 8(SP) for the link register and 4(SP) for the condition register. The called routine may save its LR and CR in the calling routine's frame. One might shrink jump_fcontext's frame from 244 to 240 bytes by saving its LR and CR in its caller's frame.  
  
@iains The 244-byte stack frame belongs to jump_fcontext, where it does `subi r1, r1, 244` and `addi r1, r1, 224`; but ontop_fcontext and make_fcontext use the same frame.  
  
When I said, "PPC32 compilers tend to disable altivec", I meant that they don't emit altivec instructions. In the ABI, each function must preserve altivec registers v20 to v31 (_AltiVec Technology Programming Interface Manual_ ALTIVECPIM.pdf, 3.2 Register Usage Conventions; example in [Apple's _setjmp](https://github.com/apple-oss-distributions/Libc/blob/Libc-391.2.10/ppc/sys/_setjmp.s)). Our jump_fcontext is missing code to save and restore v20..v31, but if the compiler didn't emit altivec, then the program never uses v20..v31, and never needs jump_fcontext to preserve v20..v31.  
  
Some libraries, like libjpeg-turbo and pixman, have code that checks if the CPU has altivec, then calls altivec code. After they return from the altivec code, they would stop using v20..v31, so there would be no problem with v20..v31. I fear a problem with a misaligned stack pointer. A fiber with a misaligned SP might fail to do jpeg or pixman.

---

## Comment 6

> Username: barracuda156  
> Created at: 2022-11-27 22:58:13 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1328360894  

@kernigh With your patch (and also adjusting 244 to 240, I did that earlier before your comment and wanted to see what happens anyway), Fibonacci test passes fine:  
```  
sergey-fedorovs-power-mac-g5:boost svacchanda$ /opt/local/bin/g++-mp-12 fibonacci.cpp -I/opt/local/libexec/boost/1.76/include  -std=c++17 -lboost_context-mt -L/opt/local/libexec/boost/1.76/lib -g -O0  
sergey-fedorovs-power-mac-g5:boost svacchanda$ sudo /opt/local/bin/gdb-apple ./a.out  
sudo: ignoring time stamp from the future  
Password:  
GNU gdb 6.3.50.20050815-cvs (Sun Oct 30 18:17:59 UTC 2022)  
Copyright 2004 Free Software Foundation, Inc.  
GDB is free software, covered by the GNU General Public License, and you are  
welcome to change it and/or distribute copies of it under certain conditions.  
Type "show copying" to see the conditions.  
There is absolutely no warranty for GDB.  Type "show warranty" for details.  
This GDB was configured as "--host=powerpc-apple-darwin10.0.0d2 --target="...Reading symbols for shared libraries .  
warning: Could not find object file "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost176/boost176/work/boost_1_76_0/bin.v2/libs/context/build/darwin-12.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/posix/stack_traits.o" - no debug information available for "libs/context/src/posix/stack_traits.cpp".  
  
..... done  
  
(gdb) run  
Starting program: /Users/svacchanda/Dev/boost/a.out   
Reading symbols for shared libraries +++++warning: Could not find object file "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost176/boost176/work/boost_1_76_0/bin.v2/libs/chrono/build/darwin-12.2.0/release/threading-multi/visibility-hidden/chrono.o" - no debug information available for "libs/chrono/src/chrono.cpp".  
  
warning: Could not find object file "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost176/boost176/work/boost_1_76_0/bin.v2/libs/chrono/build/darwin-12.2.0/release/threading-multi/visibility-hidden/thread_clock.o" - no debug information available for "libs/chrono/src/thread_clock.cpp".  
  
warning: Could not find object file "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost176/boost176/work/boost_1_76_0/bin.v2/libs/chrono/build/darwin-12.2.0/release/threading-multi/visibility-hidden/process_cpu_clocks.o" - no debug information available for "libs/chrono/src/process_cpu_clocks.cpp".  
  
.warning: Could not find object file "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost176/boost176/work/boost_1_76_0/bin.v2/libs/thread/build/darwin-12.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/pthread/thread.o" - no debug information available for "libs/thread/src/pthread/thread.cpp".  
  
warning: Could not find object file "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost176/boost176/work/boost_1_76_0/bin.v2/libs/thread/build/darwin-12.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/pthread/once.o" - no debug information available for "libs/thread/src/pthread/once.cpp".  
  
warning: Could not find object file "/opt/local/var/macports/build/_opt_PPCSnowLeopardPorts_devel_boost176/boost176/work/boost_1_76_0/bin.v2/libs/thread/build/darwin-12.2.0/release/threadapi-pthread/threading-multi/visibility-hidden/future.o" - no debug information available for "libs/thread/src/future.cpp".  
  
... done  
  
Program exited normally.  
```  
No more Bus errors, awesome!  
  
Two questions:  
  
1. So what will be the optimal way to save hidden, given alignment requirement?  
  
2. To what extent this code can be used for Darwin `ppc64` (of course with adjusting to 64-bit registers and replacing some insns with 64-bit equivalents)? Also, current `ppc64` code in Boost does not save FP registers at all. Not sure that is what we want.

---

## Comment 7

> Username: evanmiller  
> Created at: 2022-12-24 13:32:35 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1364530912  

@barracuda156 The fibonacci program should print out the first 10 numbers from the Fibonacci sequence. I'm not seeing them in your output, so I wouldn't yet call the test passing.

---

## Comment 8

> Username: evanmiller  
> Created at: 2022-12-26 16:28:58 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1365276053  

All right, I've been twisting my brain up with this code this morning trying to make sense of it... Disclaimer, I'm not an assembly programmer or systems expert, just making sense of what I can from the links in this thread.  
  
I *think* the additional change needed in `ontop_ppc32_sysv_macho_gas.S` amounts to this:  
  
```diff  
     ; restore CTR  
     mtctr r6  
   
+    ; set the first arg to the on-top function (r5 is already correct)  
+    mr r4, r7  
+  
     ; jump to ontop-function  
     bctr  
```  
  
Basically, the on-top function takes a `transfer_t` and returns a `transfer_t`. Because `transfer_t` is a composite type, the return value will go into `r3`, but it needs to be passed as the first two registers. In this case, that's `R4:R5` rather than `R3:R4` that we saw in the `make_fcontext` callback since `r3` is now being used for a return value. R5 is correctly set at this point in the code, so all we need to do is get R4 right.  
  
I'll test this code locally once I get my Boost environment set up on my test machine... In the meantime, @barracuda156 feel free to try it out since you seem to have everything up and running. Btw I strongly suggest NOT making the 244 -> 240 change you mentioned without fully understanding all the implications.

---

## Comment 9

> Username: barracuda156  
> Created at: 2022-12-26 19:30:07 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1365400055  

@evanmiller I am temporarily away from native hardware, but ppc32 can be tested in Rosetta with reasonable reliability. Will try that in coming days.

---

## Comment 10

> Username: barracuda156  
> Created at: 2022-12-26 19:33:32 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1365400802  

@kernigh When you have time, could you please comment on ppc64 versions? Is there something special to be done or just adjust byte sizes?  
  
Hopefully we can fix the whole Darwin PPC implementation in one go (after all) and not leave ppc64 for later.

---

## Comment 11

> Username: evanmiller  
> Created at: 2022-12-26 21:32:27 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1365472965  

Good news! Well, mixed...  
  
Compiling with the two changes above (@kernigh's plus my register tweak), I *am* able to get the Fibonacci sequence to print!  
  
```  
$ ./a.out  
0 1 1 2 3 5 8 13 21 34  
main: done  
fish: Job 1, './a.out', terminated by signal SIGSEGV (Address boundary error)  
```  
  
This means that control is successfully passed several times between a fiber and the main program. Hooray!  
  
*However*, as you can see, there's a segfault when the program ends... I am guessing it is triggered during `fiber_exit`, and perhaps my change to `ontop_fcontext` was incorrect or insufficient. I'll need to play around with a debugger to get some more information here. More later...

---

## Comment 12

> Username: evanmiller  
> Created at: 2022-12-27 15:44:02 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1365999191  

OK, I think I've gotten it working now... see #215 for the latest and greatest.  
  
In addition to @kernigh's `make_fcontext` patch above, I studied his patch in #123 to fix the `ontop_fcontext` code. Basically the Darwin code suffered from the same problems as the Linux code: the incorrect `transfer_t` was being written to the return value, and C++ exceptions were not properly handled, resulting in a crash when the fiber was being deallocated (Boost deliberately throws a C++ exception to unwind the fiber stack). The solution was to mirror the Linux code, calling out to the C++ tail function (which calls the ontop-function), rather than calling the ontop-function directly. Except for the incorrectly `transfer_t` destination, the registers were already correctly set up for this function call, so the changes to `ontop_fcontext` ended up being quite small.  
  
The Fibonacci test now prints correct output, and doesn't crash, so I'm pretty pleased with it. Once I finish fiddling with the file structure I'll remove the Draft status. Stack alignment and PPC64 I will leave to others, or else save for another holiday.

---

## Comment 13

> Username: evanmiller  
> Created at: 2022-12-27 17:03:00 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1366047296  

Btw it looks like the entire Darwin PPC64 `make_fcontext` function was accidentally commented out in this 2016 commit, and never restored:  
  
https://github.com/boostorg/context/commit/ba357205a9968bc500563ade2fcdf23fb44c86a8#diff-4812b5eb7dffd2775492b2b706f826bf1463a239399169eb1711ffec9085a49bL53  
  
So PPC64 "needs some work", to say the least.

---

## Comment 14

> Username: barracuda156  
> Created at: 2022-12-30 21:42:57 UTC  
> Updated at: 2022-12-30 21:44:12 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1368099152  

@evanmiller @kernigh @olk A quick update. `gcc12` does no help, and `fiber` fails to build with the new fixes to `context` from https://github.com/boostorg/context/pull/215  
  
Specifically, these components of `fiber` fail to compile (with assembler errors):  
```  
barrier  
condition_variable  
context  
mutex  
recursive_mutex  
recursive_timed_mutex  
timed_mutex  
scheduler  
waker  
algo/work_stealing  
```

---

## Comment 15

> Username: barracuda156  
> Created at: 2023-05-15 08:12:19 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1547393055  

> @evanmiller @kernigh @olk A quick update. `gcc12` does no help, and `fiber` fails to build with the new fixes to `context` from #215  
>   
> Specifically, these components of `fiber` fail to compile (with assembler errors):  
>   
> ```  
> barrier  
> condition_variable  
> context  
> mutex  
> recursive_mutex  
> recursive_timed_mutex  
> timed_mutex  
> scheduler  
> waker  
> algo/work_stealing  
> ```  
  
`fiber` will be fixed once this is merged: https://github.com/boostorg/fiber/pull/306

---

## Comment 16

> Username: barracuda156  
> Created at: 2023-05-15 08:27:00 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1547413847  

> Btw it looks like the entire Darwin PPC64 `make_fcontext` function was accidentally commented out in this 2016 commit, and never restored:  
>   
> [ba35720#diff-4812b5eb7dffd2775492b2b706f826bf1463a239399169eb1711ffec9085a49bL53](https://github.com/boostorg/context/commit/ba357205a9968bc500563ade2fcdf23fb44c86a8#diff-4812b5eb7dffd2775492b2b706f826bf1463a239399169eb1711ffec9085a49bL53)  
>   
> So PPC64 "needs some work", to say the least.  
  
@evanmiller Sorry, where in particular?

---

## Comment 17

> Username: evanmiller  
> Created at: 2023-05-15 11:31:15 UTC  
> Updated at: 2023-05-15 11:32:47 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1547683954  

> > Btw it looks like the entire Darwin PPC64 `make_fcontext` function was accidentally commented out in this 2016 commit, and never restored:  
> > [ba35720#diff-4812b5eb7dffd2775492b2b706f826bf1463a239399169eb1711ffec9085a49bL53](https://github.com/boostorg/context/commit/ba357205a9968bc500563ade2fcdf23fb44c86a8#diff-4812b5eb7dffd2775492b2b706f826bf1463a239399169eb1711ffec9085a49bL53)  
> > So PPC64 "needs some work", to say the least.  
>   
> @evanmiller Sorry, where in particular?  
  
See line linked in above comment – the entire PPC64 file was accidentally commented out in 2016, so no one has compiled it in years.  
  
Line 53 of src/asm/make_ppc64_sysv_macho_gas.S  
  
<img width="584" alt="image" src="https://github.com/boostorg/context/assets/134711/806ac37a-8b09-4e32-85b0-554b792aa269">

---

## Comment 18

> Username: Kojoley  
> Created at: 2023-05-30 19:33:46 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1568974986  

> the entire PPC64 file was accidentally commented out in 2016, so no one has compiled it in years.  
  
Is there is a way to cross-compile to that target from a modern macos? It would be nice to have a CI job.

---

## Comment 19

> Username: iains  
> Created at: 2023-05-30 19:44:23 UTC  
> Updated at: 2023-05-30 19:47:14 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1568989862  

> > the entire PPC64 file was accidentally commented out in 2016, so no one has compiled it in years.  
>   
> Is there is a way to cross-compile to that target from a modern macos? It would be nice to have a CI job.  
  
Yes, it can be done - but requires:  
  
 * a set of "binutils" capable of targeting powerpc-darwin (which means building them locally and is a little fiddly at present).   
   
 * A MacOSX 10.5 SDK on the build machine (there are no PPC64 slices in later SDK versions and no PPC slices after 10.6)  
   
Having said that,  I do this reasonably often from x86-64-darwin2* (and earlier).  
  
It is also actually possible at least to run compile tests on the cross-compiler (with TCL and dejagnu needed), and if you have a real hardware box, execute tests can be run remotely (although it is much better performance to do the build locally if you have decent hardware)  
  
edit: "binutils" == cctools, ld64 and a version of dsymutil built from the LLVM sources (with powerpc patched in)... hence the "bit fiddly" - but, nevertheless doable.

---

## Comment 20

> Username: Kojoley  
> Created at: 2023-05-30 20:05:52 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1569018675  

> * a set of "binutils" capable of targeting powerpc-darwin (which means building them locally and is a little fiddly at present).  
  
AppleClang is stripped from PPC support?  
  
I don't have hardware nor (would) use it. I'm planning on reworking asm compiling to dispatch targets via preprocessing (https://github.com/boostorg/context/compare/develop...Kojoley:context:feature/autodispatch) and having as much targets as possible on CI would reduce chances of breaking your targets. I think my case would be covered by just testing if `b2 asm_sources` succeeds and that requires only `clang --target=... -x assembler-with-cpp foo.S -o foo.o` to work.

---

## Comment 21

> Username: iains  
> Created at: 2023-05-30 20:12:51 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1569027926  

Apple clang never supported PPC (at least, no released or published version did).  Neither does upstream clang (without patches and that support is incomplete).  The last Apple toolchain with  PPC support is the Xcode 3.2.6 gcc-4.2.1.  
  
I was talking about using a GCC cross toolchain (which continues to support powerpc-darwin, even with current trunk [14.0.0]).  
  
Having said that, I realise you need a pretty restricted set of functionality - essentially preprocessor + assembler, right?  
  
(I will see if there's some easier sub-set of tools to do that - but you can be certain that there is nothing available 'out of the box').

---

## Comment 22

> Username: Kojoley  
> Created at: 2023-05-30 20:16:22 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1569033986  

> Having said that, I realise you need a pretty restricted set of functionality - essentially preprocessor + assembler, right?  
  
Yes, though I forgot that you guys added `asm/tail_ontop_ppc32_sysv.cpp` thing.  
  
> (I will see if there's some easier sub-set of tools to do that - but you can be certain that there is nothing available 'out of the box').  
  
I will just ping here to test the PR then.

---

## Comment 23

> Username: barracuda156  
> Created at: 2023-05-31 00:54:42 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1569335702  

> edit: "binutils" == cctools, ld64 and a version of dsymutil built from the LLVM sources (with powerpc patched in)... hence the "bit fiddly" - but, nevertheless doable.  
  
Are these `darwin-xtools` or some other modification?

---

## Comment 24

> Username: Kojoley  
> Created at: 2023-06-14 02:38:49 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1590356458  

> > (I will see if there's some easier sub-set of tools to do that - but you can be certain that there is nothing available 'out of the box').  
>   
> I will just ping here to test the PR then.  
  
Here is the PR https://github.com/boostorg/context/pull/228 if you care enough to test it.

---

## Comment 25

> Username: olk  
> Created at: 2023-08-18 05:05:16 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1683358282  

should be work now

---

## Comment 26

> Username: barracuda156  
> Created at: 2023-08-18 05:12:19 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1683362355  

@olk I guess, `ppc64` still broken though? I have not seen anyone fix it.

---

## Comment 27

> Username: olk  
> Created at: 2023-08-18 05:19:45 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1683367149  

> @olk I guess, `ppc64` still broken though? I have not seen anyone fix it.  
  
deutsch I assumed it is fixed ... maybe I was confused be the lengthy discussion

---

## Comment 28

> Username: barracuda156  
> Created at: 2023-08-18 14:29:44 UTC  
> Updated at: 2023-08-18 14:30:52 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-1684005193  

@olk Last time I looked at it – it was broken to the point of one of the source files being accidentally commented out, and that for years went unnoticed :)   
Ah, just above: https://github.com/boostorg/context/issues/211#issuecomment-1547683954  
  
It is trivial to make it build, however Fibonacci test fails. Someone with a better understanding of assembler and ABI has to look into it.

---

## Comment 29

> Username: barracuda156  
> Created at: 2025-03-28 11:16:30 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-2761060362  

> > the entire PPC64 file was accidentally commented out in 2016, so no one has compiled it in years.  
>   
> Is there is a way to cross-compile to that target from a modern macos? It would be nice to have a CI job.  
  
@Kojoley I think @Jazzzny has built arm64 to powerpc cross-compiler recently (on macOS).

---

## Comment 30

> Username: iains  
> Created at: 2025-03-28 15:35:40 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-2761712516  

> > > the entire PPC64 file was accidentally commented out in 2016, so no one has compiled it in years.  
> >   
> >   
> > Is there is a way to cross-compile to that target from a modern macos? It would be nice to have a CI job.  
  
I fairly regularly cross-compile from x86_64-darwin21 -> powerpc-darwin9 (but I have a set of cctools/ld64 that runs on modern darwin ..[on the TODO to post them]).  So yes (modulo requiring the tools + SDK for 10.5) it works.

---

## Comment 31

> Username: fds242  
> Created at: 2026-02-01 09:44:10 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3830732621  

Attaching corrected, working versions of `jump_ppc64_sysv_macho_gas.S` and `make_ppc64_sysv_macho_gas.S` here: [jump-make_make_ppc64_sysv_macho_gas.zip](https://github.com/user-attachments/files/24991224/jump-make_make_ppc64_sysv_macho_gas.zip)  
  
I realize that it's incomplete in that I've not touched the `ontop_ppc64_sysv_macho_gas.S` file, and also that that code is clearly broken too. Admittedly my interest was in getting my ppc64 build of PHP working correctly, and PHP does not use the ontop stuff. Submitting it in this comment anyway in case somebody else might find even this much useful, or runs with it, and completes the job.

---

## Comment 32

> Username: barracuda156  
> Created at: 2026-02-01 14:45:52 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3831147415  

@fds242 Thank you! Do you just lack a test case for `ontop` one or not interested to deal with it? Would you be willing to make a PR (as a draft first)?  
  
P. S. I _think_ by convention labels are in a form of `L`+number (i.e. with capital L).

---

## Comment 33

> Username: iains  
> Created at: 2026-02-01 14:55:48 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3831159757  

> P. S. I _think_ by convention labels are in a form of `L`+number (i.e. with capital L).  
  
It is fine to have a more informative label name.  The important point is that it starts with "L" to be private to the assembly file.  
  
Symbols that do not start with "L" will be present in the object file symbol table and visible to the linker - so only do that if you intend them to be so-visible.

---

## Comment 34

> Username: barracuda156  
> Created at: 2026-02-01 14:57:16 UTC  
> Updated at: 2026-02-01 14:59:18 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3831161328  

>  The important point is that it starts with "L" to be private to the assembly file.  
  
@iains That was my point, though I was not aware of the actual reason for that. Thank you for explaining.  
  
Besides, maybe you could look into ppc64 assembler here? There are three files with not much code inside. I am aware that you cannot test anything for ppc64 atm, but that we can handle.

---

## Comment 35

> Username: fds242  
> Created at: 2026-02-01 17:56:51 UTC  
> Updated at: 2026-02-01 19:59:02 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3831612042  

@barracuda156 As for the labels, those are entirely untouched by me, and the same as the ppc32 version. Basically, I completely ignored the existing ppc64/darwin files since they were broken, and instead made a copy of the known-working ppc32/darwin files, as originally created in this very same bug report.  
Then with a reading of the also afore-linked here archived Apple ABI documentation, I did my best to change precisely only as much in them as was needed to turn them into functioning on ppc64 instead. Even whitespace and all indentation is left the same as in the ppc32 .S files, mostly for my benefit so I could easily diff what I've changed.  
  
A key change in the ppc32 and ppc64 ABI on darwin, as I've come to learn in this process, concerns the return of that `transfer_t` struct with two pointers in it. On ppc64/darwin you can conveniently return such a two-element structure in r3 and r4, making the ppc64 code actually simpler. In the ppc32 ABI, the caller sends a pointer to save the struct in via an implicit extra input parameter in r3. This shifts all the other input parameters by one into a later register on ppc32, which you also can and have to undo (r4 => r3, r5 => r4, etc.). A cursory glance at the `ontop_` file shows none of this was done there, and it still assumes the transfer_t struct is returned in the ppc32 ABI way.  
  
So I can see it's broken, and would also need to be fixed for a proper solution to this bug report. But I've never actually used Boost, built Boost, or Boost.context in any way. I haven't even tested my changes through Boost/C++ which is why I'm reluctant to even post any of this a PR myself. I'd rather someone who actually tests it with Boost code did it. Thank you in advance.  
  
What I do build and use is PHP. PHP 8.1, in 2021, imported the `make_*` and `jump_*` files from this project verbatim into Zend/asm, using it to implement the Fibers feature in PHP. Ever since then, I was acutely aware that my ppc64/darwin builds of PHP never fully worked. Yes, I've lived through -- and immediately noticed -- the era where the ppc64 file was one big unterminated comment, too. :) Finally, this weekend in 2026 so many years later I scratched this old itch, and now everything's fixed as far as my ppc64 build of PHP needed it.  
  
All that is to say, while it would be only right for me to commit to fully fixing the rest of this issue here in Boost.context, I've rather serious doubts as to when, if ever, I would get around to it. My primary encouragement for doing so would be the fact that I do have a fine modern GCC 15.2 ready to cross-compile even C++23 to ppc64/darwin, with all credit due to the amazing work of @iains. What an absolute legend!

---

## Comment 36

> Username: barracuda156  
> Created at: 2026-02-01 18:38:32 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3831735500  

@fds242 Thank you for a detailed explanation. Indeed, in this case it makes little sense to make a PR, if the changes can only be tested in PHP codebase. I can try with the current release of boost (and reference your fixes, if everything works, in a PR).  
  
I will look into the labels issue, maybe we need to fix that for ppc32 then.  
  
Have you got your port of PHP for powerpc-darwin somewhere? I recall PHP had some issues, though I had it building last time I tried. (You are also most welcome to submit a PR for PHP into https://github.com/macos-powerpc/powerpc-ports repo.)

---

## Comment 37

> Username: iains  
> Created at: 2026-02-01 21:09:28 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3832041959  

> [@fds242](https://github.com/fds242) Thank you for a detailed explanation. Indeed, in this case it makes little sense to make a PR, if the changes can only be tested in PHP codebase. I can try with the current release of boost (and reference your fixes, if everything works, in a PR).  
  
You should both note that the PPC64 ABI (calling convention) is _**very**_ different from the PPC32 one (in particular, it allows structures to be decomposed into registers on both call and return).  
  
However, (very very quick look) these seem to be context save & restore functions - and so special consideration might be needed... sorry, I don't have any spare cycles to look right now ... I will see if I can find an example from the GCC codebase (I might have done one for D).  
  
> I will look into the labels issue, maybe we need to fix that for ppc32 then.  
  
Looking at the two attached files there does not seem to be any issue with labels.  
  
```  
text  
.private_extern _jump_fcontext  
.globl _jump_fcontext  
.align 3  
_jump_fcontext:  
```  
This is fine .. an external entry point.  
  
```  
L1:  
    ; load LR into R4  
    mflr  r4  
    ; compute abs address of label finish  
    addis r5, r4, ha16(Lfinish - L1)  
    addi  r5, r5, lo16(Lfinish - L1)  
    ; restore LR  
    mtlr  r0  
    ; save address of finish as return-address for context-function  
    ; will be entered after context-function returns  
    std  r5, 320(r3)  
  
    ; restore return address from R6  
    mtlr  r6  
  
    blr  ; return pointer to context-data  
  
Lfinish:  
    ; load address of _exit into CTR  
    bcl  20, 31, L2  
L2:  
    mflr  r4  
    addis  r4, r4, ha16(Lexitp - L2)  
    ld  r4, lo16(Lexitp - L2)(r4)  
    mtctr  r4  
    ; exit code is zero  
    li  r3, 0  
    ; exit application  
    bctr  
  
.const_data  
.align 3  
Lexitp:  
```  
  
` Lfinish, Lexitp, L1 and L2 ` are all legitimate TU-local labels - however, you should check that you are getting a relocation for the references to `Lexitp` since that's in a different section.

---

## Comment 38

> Username: barracuda156  
> Created at: 2026-02-01 21:13:47 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3832046977  

> Looking at the two attached files there does not seem to be any issue with labels.  
  
I just woke up and the brain was defunct LOL – I was comparing it reverse way for some reason. I.e., what I referred to was: https://github.com/boostorg/context/blob/639fa73056578dbffc82537854109430950ed3ef/src/asm/make_ppc64_sysv_macho_gas.S#L100  
Attached files are okay, of course. Sorry for a confusion.

---

## Comment 39

> Username: fds242  
> Created at: 2026-02-01 22:22:45 UTC  
> Updated at: 2026-02-01 23:39:37 UTC  
> Url: https://github.com/boostorg/context/issues/211#issuecomment-3832160757  

@barracuda156 Ah, good to see the labels question cleared up. :) I’ve tested everything quite extensively on actual hardware, though only as invoked through PHP’s C fibers code. I’m reasonably confident it should work fine inside Boost as well, at least as well as the current ppc32 variant. (Better, in part, since it doesn’t misalign the stack in jump_fcontext. 😉) I’m looking forward to your Boost test results.  
Except if that test touches the ‘ontop’ code, which I’m not exactly sure when that is needed, since that part remains broken for ppc64.  
  
I no longer presently share my binaries anywhere, for lack of interest, or usefulness to the general public. More than happy to share anything, or any knowledge gained, where it wouldn’t be off-topic (a PM, I suppose), of course.  
I’m not a powerpc enthusiast per se. What I do is all-architecture ppc/ppc64/i386/x86_64/arm64 truly universal builds of PHP, and every single command tool I use day-to-day personally, for the target audience of my one weird self. Back in the early 2000s when an iMac G5 joined my first ever Mac, a PowerBook, I established my then somewhat reasonable practice that I’d build on my fastest machine, and rsync /usr/local to all others. Then I stubbornly never stopped, even as years, decades, and more achitectures kept getting added on. As the binaries became ever fatter, so did my person, and the rule never changed: all my /usr/local is still synced, and every Mac I’ve ever owned should keep being up to date. From my belongs-in-a-museum very first to my newest Apple silicon M4.  
  
That I’m still at it all these decades later, even capable of such a thing, is of course through no thanks to Apple Inc, and in major part all thanks to the work of @iains.
