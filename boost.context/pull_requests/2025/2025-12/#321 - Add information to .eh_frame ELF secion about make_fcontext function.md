# #321 Add information to .eh_frame ELF secion about make_fcontext function. [Merged]

> Username: vchigrin  
> Created at: 2025-12-10 11:57:49 UTC  
> Updated at: 2025-12-27 16:25:41 UTC  
> Merged at: 2025-12-27 16:25:36 UTC  
> Closed at: 2025-12-27 16:25:36 UTC  
> Url: https://github.com/boostorg/context/pull/321  

Clang version of libunwind uses long linear scan of the .eh_frame section if it fails to find frame information in .eh_frame_hdr.  
That causes long times of stack unwinding in case when current stack has frames with missed FDE records, so it is better to have information about all frames in the stack up until frame which marks return address ad "undefined/unrecoverable".

---

## Comment 1

> Username: vchigrin  
> Created_at: 2025-12-10 11:59:52 UTC  
> Url: https://github.com/boostorg/context/pull/321#issuecomment-3636747430  

I am working on PR to clang to make stack walking times better in case of missed frames (See https://github.com/llvm/llvm-project/pull/167849 ). But while generic solution is not ready, small local fix here seems resasonable.

---

## Comment 2

> Username: vchigrin  
> Created_at: 2025-12-25 13:39:57 UTC  
> Url: https://github.com/boostorg/context/pull/321#issuecomment-3691453342  

On our test binary these changes speed up stack unwinding rather strong. Time of construction of `boost::stacktrace::stacktrace` object in coroutine was:  
  
Before changes: 5071µs  
After changes: 82µs

---

## Comment 3

> Username: olk  
> Created_at: 2025-12-27 16:25:41 UTC  
> Url: https://github.com/boostorg/context/pull/321#issuecomment-3694075576  

ty

---
