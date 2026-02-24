# #11 Performed unaligned loads and stores on x86 directly. [Closed]

> Username: jbms  
> Created at: 2015-01-04 20:31:20 UTC  
> Updated at: 2015-01-06 22:35:31 UTC  
> Closed at: 2015-01-06 22:11:16 UTC  
> Url: https://github.com/boostorg/endian/pull/11  

This makes unaligned little endian buffer types significantly more efficient.

---

## Comment 1

> Username: Beman  
> Created_at: 2015-01-05 14:17:04 UTC  
> Url: https://github.com/boostorg/endian/pull/11#issuecomment-68712829  

Great idea! Why didn't I think of this:-?  
  
I'd like to try to eliminate the run-time test for size by pushing the decision up to the load/store caller, where hopefully it can be done at compile-time.  
  
I'm actively working on Endian at the moment, so should be able to look at that later this week.  
  
Thanks,  
  
--Beman

---

## Comment 2

> Username: jbms  
> Created_at: 2015-01-05 17:38:10 UTC  
> Url: https://github.com/boostorg/endian/pull/11#issuecomment-68744268  

Since the condition is a compile-time constant, no runtime check is likely to be generated (and this indeed appears to be the case on GCC 4.9.2).  If you prefer the check to be explicitly compile-time, though, I could rework the code.  The one thing that concerns me is whether the reinterpret_cast is undefined behavior under the standard, such that compiler optimizations may screw something up.

---

## Comment 3

> Username: Beman  
> Created_at: 2015-01-05 19:14:13 UTC  
> Url: https://github.com/boostorg/endian/pull/11#issuecomment-68759489  

On Mon, Jan 5, 2015 at 12:38 PM, Jeremy Maitin-Shepard <  
notifications@github.com> wrote:  
  
> Since the condition is a compile-time constant, no runtime check is likely  
> to be generated (and this indeed appears to be the case on GCC 4.9.2). If  
> you prefer the check to be explicitly compile-time, though, I could rework  
> the code.  
>   
> Interesting. I'll run a test on VC++ to see if the same is true with that  
> compiler.  
>   
> The one thing that concerns me is whether the reinterpret_cast is  
> undefined behavior under the standard, such that compiler optimizations may  
> screw something up.  
>   
> IIUC, it should be OK. 5.2.10 para 7 says:  
  
An object pointer can be explicitly converted to an object pointer of a  
different type. When a prvalue v of object pointer type is converted to the  
object pointer type “pointer to cv T”, the result is static_cast<cv  
T*>(static_cast<cv void*>(v)). Converting a prvalue of type “pointer to T1”  
to the type “pointer to T2” (where T1 and T2 are object types and where the  
alignment requirements of T2 are no stricter than those of T1) and back to  
its original type yields the original pointer value.  
  
There are architectures where the alignment requirements differ, but we  
would only be doing this for an architecture where there is no strict  
alignment requirement.  
  
--Beman

---

## Comment 4

> Username: jbms  
> Created_at: 2015-01-05 20:09:13 UTC  
> Url: https://github.com/boostorg/endian/pull/11#issuecomment-68769886  

Even though x86 allows unaligned accesses, since the compiler defines the alignment to be greater than 1, is there potentially a problem?  
  
Maybe adding a compiler-specific packed or alignment attribute would avoid problems from the optimizer.

---

## Comment 5

> Username: Beman  
> Created_at: 2015-01-06 22:11:16 UTC  
> Url: https://github.com/boostorg/endian/pull/11#issuecomment-68943573  

Pull request applied, with some additional comments added.  
  
I'm not worried about optimizers adding unwanted padding, as that would break tons of code. But maybe the tests should run as release builds.  
  
I verified that on both 32 and 64-bit release builds VC++ 14.0 Preview elides the runtime check, just as you found with GCC. Docs have been updated, but I have not rerun timing tests yet.  
  
Thanks,  
  
--Beman

---

## Comment 6

> Username: jbms  
> Created_at: 2015-01-06 22:35:31 UTC  
> Url: https://github.com/boostorg/endian/pull/11#issuecomment-68947205  

Thanks for incorporating that.  By issues with the optimizer, what I meant was the issue of the compiler reordering or optimizing out loads or stores in a way that breaks program behavior.  It is hard to come up with a concrete example, though.

---
