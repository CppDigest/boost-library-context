# #65 - Assembler-Error with boost on AIX with gcc8 [Closed]

> Username: Flow86  
> Created at: 2019-03-07 10:24:31 UTC  
> Updated at: 2019-06-24 07:22:36 UTC  
> Closed at: 2019-06-24 07:22:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/65  

Hi,  
  
I am using boost on an AIX 7.2 host with IBM Linux Toolbox.   
We used the old gcc 4.6.3 until now and tried upgrading to the official gcc 8.2 version by IBM.  
  
We now get an "as: Syntax error" with the new gcc coming from th file `boost/include/boost/smart_ptr/detail/sp_counted_base_gcc_ppc.hpp`.  
  
At the moment, we're using boost 1.62, but the file was not changed on a newer boost version.  
  
Exactly, the numeric labels in the inline assembler code ("0:") is causing the problem.  
I changed the inline assembler to use non numeric labels now:  
  
```  
--- a/boost/include/boost/smart_ptr/detail/sp_counted_base_gcc_ppc.hpp  2017-06-06 18:19:26.000000000 +0200  
+++ b/boost/include/boost/smart_ptr/detail/sp_counted_base_gcc_ppc.hpp   2019-03-07 11:16:37.000000000 +0100  
@@ -40,11 +40,11 @@  
  
      __asm__  
      (  
-         "0:\n\t"  
+         "loop%=:\n\t"  
          "lwarx %1, 0, %2\n\t"  
          "addi %1, %1, 1\n\t"  
          "stwcx. %1, 0, %2\n\t"  
-         "bne- 0b":  
+         "bne- loop%=":  
  
          "=m"( *pw ), "=&b"( tmp ):  
          "r"( pw ), "m"( *pw ):  
@@ -61,11 +61,11 @@  
      __asm__ __volatile__  
      (  
          "sync\n\t"  
-         "0:\n\t"  
+         "loop%=:\n\t"  
          "lwarx %1, 0, %2\n\t"  
          "addi %1, %1, -1\n\t"  
          "stwcx. %1, 0, %2\n\t"  
-         "bne- 0b\n\t"  
+         "bne- loop%=\n\t"  
          "isync":  
  
          "=m"( *pw ), "=&b"( rv ):  
@@ -85,14 +85,14 @@  
  
      __asm__  
      (  
-         "0:\n\t"  
+         "loop%=:\n\t"  
          "lwarx %1, 0, %2\n\t"  
          "cmpwi %1, 0\n\t"  
-         "beq 1f\n\t"  
+         "beq store%=\n\t"  
          "addi %1, %1, 1\n\t"  
-         "1:\n\t"  
+         "store%=:\n\t"  
          "stwcx. %1, 0, %2\n\t"  
-         "bne- 0b":  
+         "bne- loop%=":  
  
          "=m"( *pw ), "=&b"( rv ):  
          "r"( pw ), "m"( *pw ):  
```  
  
I'm not so extremely firm with ASM (yet), so did I do that correctly?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-06-18 09:59:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/65#issuecomment-503036457  

This looks correct, even though I have no way to verify it. Would you submit a pull request?

---

## Comment 2

> Username: Flow86  
> Created at: 2019-06-19 04:59:55 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/65#issuecomment-503406576  

I still find it weird why https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/detail/sp_counted_base_aix.hpp isnt used. Perhaps it really was only a bug in the old boost version: https://github.com/boostorg/smart_ptr/commit/16084637a6b5eb188cbd07f71cddf4eca43a66c1#diff-eca044534514a8ecf9b8450be1422f9c

---

## Comment 3

> Username: pdimov  
> Created at: 2019-06-19 21:58:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/65#issuecomment-503762350  

This commit references https://svn.boost.org/trac10/ticket/6996, but it doesn't shed much light.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-06-19 22:04:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/65#issuecomment-503763784  

The `_AIX` path was mistakenly removed in https://github.com/boostorg/smart_ptr/commit/1029ae0ea5497db1a6884d129cb2bc4d9d238bc8#diff-eca044534514a8ecf9b8450be1422f9c and then reinstated in https://github.com/boostorg/smart_ptr/commit/03ae5cdbc69854d5c5c8f597153e4832b9c4cc76#diff-eca044534514a8ecf9b8450be1422f9c.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-06-19 22:06:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/65#issuecomment-503764509  

This all happened in Boost 1.56, so it should be in 1.62 too. Perhaps the new GCC doesn't define `_AIX`?

---

## Comment 6

> Username: Flow86  
> Created at: 2019-06-24 07:22:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/65#issuecomment-504890978  

it does, but I also can't reproduce it at the moment. Weird thing ...
