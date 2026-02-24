# #236 - UBSan false positive in thread_specific_ptr [Closed]

> Username: Kojoley  
> Created at: 2018-10-02 18:46:30 UTC  
> Updated at: 2019-01-03 18:10:54 UTC  
> Closed at: 2019-01-03 18:10:54 UTC  
> Url: https://github.com/boostorg/thread/issues/236  

`./b2 -j2 toolset=clang-7 cxxflags="-fsanitize=undefined -fno-sanitize-recover=undefined" linkflags="-fsanitize=undefined" libs/thread/test//ex_tss -a`  
  
```  
testing.capture-output bin.v2/libs/thread/test/ex_tss.test/clang-linux-7/debug/threadapi-pthread/threading-multi/visibility-hidden/ex_tss.run  
====== BEGIN OUTPUT ======  
libs/thread/src/pthread/thread.cpp:114:37: runtime error: member call on address 0x000001938d10 which does not point to an object of type 'boost::detail::tss_cleanup_function'  
0x000001938d10: note: object is of type 'boost::thread_specific_ptr<int>::delete_data'  
 00 00 00 00  90 f8 67 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  31 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::thread_specific_ptr<int>::delete_data'  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
**Update**:  
  - GCC ticket https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80963   
  - LLVM ticket https://bugs.llvm.org/show_bug.cgi?id=39191

---

## Comment 1

> Username: viboes  
> Created at: 2018-10-02 19:54:57 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426408232  

Hi, thanks for the report.  
  
We know that thread_specific_ptr is broken since a long time, but we don't know how to fix it.  
  
Any help is welcome, a PR, any aditional analysis, ...

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-10-02 20:06:29 UTC  
> Updated at: 2018-10-02 20:07:31 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426411665  

Sigh, I thought it is something new because previously I did not see the problem (I found it via Spirit), it should be a new feature in UBSAN or the problem did show up without adding a `-fno-sanitize-recover=undefined` flag.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-10-03 00:26:11 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426473882  

I feel like it is a bug in UBSAN, but I may be wrong because I did not succeeded in making a MWE. With #237 UBSAN is happy, but I am not sure why it works.

---

## Comment 4

> Username: pdimov  
> Created at: 2018-10-03 02:24:13 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426491543  

This is likely a false positive caused by hidden visibility. Use `visibility=global` on UBsan runs.

---

## Comment 5

> Username: Kojoley  
> Created at: 2018-10-03 15:57:38 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426691894  

@pdimov You are right, the problem is gone if I add `visibility=global`. Is it a known limitation or it is needed to be reported?

---

## Comment 6

> Username: pdimov  
> Created at: 2018-10-03 16:52:27 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426712624  

According to  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80963  
  
it's not clear if UBsan is correct here or not; in principle, with hidden visibility, two base classes of the same name exist in the program, but in C++ this can't be true due to ODR.

---

## Comment 7

> Username: pdimov  
> Created at: 2018-10-03 16:53:12 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426712876  

In either case, this is a known issue - here's @mclow encountering it in 2013: https://cplusplusmusings.wordpress.com/2013/03/26/testing-libc-with-fsanitizeundefined/

---

## Comment 8

> Username: pdimov  
> Created at: 2018-10-03 16:53:48 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426713078  

I just add `visibility=global` to the UBsan configurations. :-)

---

## Comment 9

> Username: Kojoley  
> Created at: 2018-10-03 16:58:43 UTC  
> Updated at: 2018-10-03 23:05:49 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-426714849  

> In either case, this is a known issue - here's @mclow encountering it in 2013:  
> cplusplusmusings.wordpress.com/2013/03/26/testing-libc-with-fsanitizeundefined  
  
If https://www.boost.org/development/tests/develop/Marshall-UBSAN.html is his worker, then he himself forgot about it.

---

## Comment 10

> Username: Kojoley  
> Created at: 2018-10-05 12:02:07 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-427341480  

@pdimov The GCC guys think it is not a false positive, but ODR rules violation https://www.mail-archive.com/gcc-bugs@gcc.gnu.org/msg534862.html

---

## Comment 11

> Username: pdimov  
> Created at: 2018-10-05 13:14:28 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-427361892  

That's the same bug I linked above. :-)

---

## Comment 12

> Username: Kojoley  
> Created at: 2018-10-05 13:44:32 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-427370877  

Hah, I am blind :-)  
  
What do you think about placing `__attribute__((visibility("default")))` on interface classes?

---

## Comment 13

> Username: pdimov  
> Created at: 2018-10-05 14:05:42 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-427377602  

I'm not going to do that. `visibility=global` on UBsan runs is good enough for me. Having two identical definitions doesn't violate ODR, by the definition of ODR.

---

## Comment 14

> Username: viboes  
> Created at: 2018-10-09 04:59:02 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-428059977  

Do we have some conclusion for this issue?

---

## Comment 15

> Username: Kojoley  
> Created at: 2018-10-09 10:26:16 UTC  
> Url: https://github.com/boostorg/thread/issues/236#issuecomment-428141275  

I have opened a ticket on LLVM bugtracker but by this time it has no response https://bugs.llvm.org/show_bug.cgi?id=39191
