# #133 IBM Z: Fix fcontext routines [Merged]

> Username: Andreas-Krebbel  
> Created at: 2020-02-21 08:34:44 UTC  
> Updated at: 2020-02-21 12:17:42 UTC  
> Merged at: 2020-02-21 08:59:22 UTC  
> Closed at: 2020-02-21 08:59:22 UTC  
> Url: https://github.com/boostorg/context/pull/133  

I found the current IBM Z fcontext support being broken and propose the following implementation instead.  
  
Problems with the existing implementation:  
1. jump_fcontext returns the wrong context pointer. Instead of returning the address of the saved context it returns the current stack pointer. Interestingly this makes the testsuite to always exit early with exit code 0. The second jump_fcontext always jumps to the exit stub set up in make_fcontext.  
2. The address of the hidden return slot needs to be saved as part of the context otherwise. Using the return slot of the current jump_fcontext invocation is wrong.  
3. The wrong FP registers are being saved and FPC is missing. The call-saved FP regs on IBM Z 64 bit are f8-f15. The current code saves f1, f3, f5, f7.  
  
A few details about the implementation:  
- make_fcontext explicitely sets the save slot for the hidden return pointer to zero to allow jump_fcontext to distingiush between make_fcontext generated contexts and others  
- for make_fcontext generated contexts jump_fcontext a) stores the transfer_t argument for the target function in the saved context and points r2 at it and b) allocates a register save area on the stack - an ABI requirement when calling functions on Z  
- ontop_fcontext also stores the transfer_t argument for the target function in the saved context.  
- ontop_fcontext does not handle make_fcontext generated contexts so far. So it is not possible to directly call ontop_fcontext on a make_fcontext generated context.

---

## Comment 1

> Username: olk  
> Created_at: 2020-02-21 08:59:27 UTC  
> Url: https://github.com/boostorg/context/pull/133#issuecomment-589561370  

ty

---

## Comment 2

> Username: olk  
> Created_at: 2020-02-21 09:02:37 UTC  
> Url: https://github.com/boostorg/context/pull/133#issuecomment-589562444  

examples are working?

---

## Comment 3

> Username: Andreas-Krebbel  
> Created_at: 2020-02-21 09:38:45 UTC  
> Url: https://github.com/boostorg/context/pull/133#issuecomment-589576173  

> examples are working?  
  
I did run "../../../b2" and "../../../b2 fc" in the test directory for the debug and the release variant. Looked all good. I also single stepped single testcases to prevent me from falling into the same trap as the previous implementation.  
  
If you have some additional tests I could check please let me know.

---

## Comment 4

> Username: olk  
> Created_at: 2020-02-21 09:52:02 UTC  
> Url: https://github.com/boostorg/context/pull/133#issuecomment-589580955  

> I did run "../../../b2" and "../../../b2 fc" in the test directory for the debug and the release variant. Looked all good.  
  
fine  
  
> If you have some additional tests I could check please let me know.  
  
sub-directory `examples` contains several small apps that show output - if you would execute these apps ...

---

## Comment 5

> Username: Andreas-Krebbel  
> Created_at: 2020-02-21 10:38:22 UTC  
> Url: https://github.com/boostorg/context/pull/133#issuecomment-589598178  

> sub-directory `examples` contains several small apps that show output - if you would execute these apps ...  
  
The two "segmented" examples in callcc and fiber failed after running for some time but that's probably expected without using the segmented stack. The rest appears to work fine. I've also checked with the previous implementation. As expected everything exited immediately.

---

## Comment 6

> Username: olk  
> Created_at: 2020-02-21 12:17:42 UTC  
> Url: https://github.com/boostorg/context/pull/133#issuecomment-589629926  

> The two "segmented" examples in callcc and fiber failed after running for some time but that's probably expected without using the segmented stack.  
  
yes, 'segmented` example should fail if not compiled with support for segmented stacks  
  
> The rest appears to work fine. I've also checked with the previous implementation. As expected everything exited immediately.  
  
very nice, thank you

---
