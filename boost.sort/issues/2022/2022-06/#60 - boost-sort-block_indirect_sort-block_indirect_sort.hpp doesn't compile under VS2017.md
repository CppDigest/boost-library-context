# #60 - boost/sort/block_indirect_sort/block_indirect_sort.hpp doesn't compile under VS2017 [Open]

> Username: PavelCelba  
> Created at: 2022-06-13 14:57:15 UTC  
> Updated at: 2023-01-29 00:34:40 UTC  
> Url: https://github.com/boostorg/sort/issues/60  

Including #include "boost/sort/block_indirect_sort/block_indirect_sort.hpp" and using boost::sort::block_indirect_sort doesn't compile on VS2017 (C++17 compiler). There are following errors:  
  
1>c:\views\conan\BoostHeaders\3.0.0+1.75.0\rs3\stable\package\5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9\include\boost/sort/common/spinlock.hpp(72): error C2065: 'not': undeclared identifier  
1>c:\views\conan\BoostHeaders\3.0.0+1.75.0\rs3\stable\package\5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9\include\boost/sort/common/spinlock.hpp(72): error C2146: syntax error: missing ';' before identifier 'af'  
  
Furthermore including: #include <boost/range/algorithm.hpp> along causes more errors since boost::sort is now template method and also internal namespace name used by block_indirect_sort.hpp header.  
  
This is in boost 1.75.0, but I don't see it fixed in current version either (by brief look).

---

## Comment 1

> Username: Morwenn  
> Created at: 2022-06-14 07:39:47 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1154828811  

That's because MSVC doesn't support alternative keywords (`not`, `and`, `or`, etc.) in its default mode. It can probably be fixed in Boost.Sort itself, but in the meantime you can either compile with `/permissive-` or include `<ciso646>` before the Boost.Sort header (this small header defines the alternative keywords in MSVC).

---

## Comment 2

> Username: PavelCelba  
> Created at: 2022-06-14 07:43:28 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1154832391  

Yes, I know. I'm working on in-house library for big project. It's not so easy to mingle with compiler settings for whole codebase. So I'll in meantime use parallel sort from std. The other issue with sort method / namespace clash is however even more pesky.  
  
Also to note there is a standard implementation of spinlock in boost. It maybe good idea to switch to that one instead of this custom and uncompilable one :-)

---

## Comment 3

> Username: fjtapia  
> Created at: 2022-06-14 11:18:55 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1155049870  

Probably, the solution is as easy as editing the file spinlock.hpp and  
adding #include <ciso646>.  
  
The use of and, or, xor not instead of && || ^ or !, is included in the C++  
standard, and the compilers support it natively, except Microsoft. If you  
want to use Visual C++, you must include the file ciso646. In other  
compilers, this file exists but it is empty.  
  
I developed the code with GCC. When finished, I compiled with CLANG without  
problems. But when I tried to compile with VC++ 2019, appear a lot of  
errors, all related to the use of and, or. After a long and stupid process,  
I included the file in the headers, and all the code (test, examples, and  
benchmarks) compile and run correctly.  
You are using an older version, I suppose you are suffering the same.  
I just installed the last version of VC++, and continue with the same  
problem.  
  
I will change the spinlock file in the repository adding the ciso646. If  
the error persists, please, say me and I will check in deep to detect and  
correct it.  
Thanks for your interest  
  
Francisco  
  
El lun, 13 jun 2022 a las 18:03, PavelCelba ***@***.***>)  
escribió:  
  
> Including #include  
> "boost/sort/block_indirect_sort/block_indirect_sort.hpp" and using  
> boost::sort::block_indirect_sort doesn't compile on VS2017 (C++17  
> compiler). There are following errors:  
>  
> 1>c:\views\conan\BoostHeaders\3.0.0+1.75.0\rs3\stable\package\5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9\include\boost/sort/common/spinlock.hpp(72):  
> error C2065: 'not': undeclared identifier  
> 1>c:\views\conan\BoostHeaders\3.0.0+1.75.0\rs3\stable\package\5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9\include\boost/sort/common/spinlock.hpp(72):  
> error C2146: syntax error: missing ';' before identifier 'af'  
>  
> Furthermore including: #include <boost/range/algorithm.hpp> along causes  
> more errors since boost::sort is now template method and also internal  
> namespace name used by block_indirect_sort.hpp header.  
>  
> This is in boost 1.75.0, but I don't see it fixed in current version  
> either (by brief look).  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/60>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GFN3IHZEUV3MHLAP5DVO5LUHANCNFSM5YUXC2EQ>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 4

> Username: PavelCelba  
> Created at: 2022-06-14 11:31:35 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1155062009  

That's nice. Thou the other problem needs fixing too (which maybe harder to fix somehow):   
Furthermore including: #include <boost/range/algorithm.hpp> along causes more errors since boost::sort is now template method and also internal namespace name used by block_indirect_sort.hpp header.

---

## Comment 5

> Username: fjtapia  
> Created at: 2022-06-14 12:22:46 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1155111914  

The two errors are in the same line.  
Probably, fixing the first, fix the second.  
If it's not true. Say me and I will check in deep  
  
El mar, 14 jun 2022 a las 13:31, PavelCelba ***@***.***>)  
escribió:  
  
> That's nice. Thou the other problem needs fixing too (which maybe harder  
> to fix somehow):  
> Furthermore including: #include <boost/range/algorithm.hpp> along causes  
> more errors since boost::sort is now template method and also internal  
> namespace name used by block_indirect_sort.hpp header.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/60#issuecomment-1155062009>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDIJHWZLFF45MN4ZV3VPBUSFANCNFSM5YUXC2EQ>  
> .  
> You are receiving this because you commented.Message ID:  
> ***@***.***>  
>

---

## Comment 6

> Username: PavelCelba  
> Created at: 2022-06-14 12:27:04 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1155116601  

For this:  
>> Furthermore including: #include <boost/range/algorithm.hpp> along causes more errors since boost::sort is now template method and also internal namespace name used by block_indirect_sort.hpp header.  
  
I have not included error messages in the first comment. But the problem is definitely there as described. And it's a different one!

---

## Comment 7

> Username: PavelCelba  
> Created at: 2022-06-14 12:51:11 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1155144234  

Reproducible for second problem - it's just enough to include files in some cpp and compile:  
#include "boost/sort/block_indirect_sort/block_indirect_sort.hpp"  
#include <boost/range/algorithm.hpp>  
  
In this order it produces:  
1>c:\views\conan\BoostHeaders\3.0.0+1.75.0\rs3\stable\package\5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9\include\boost/range/algorithm/sort.hpp(65): error C2874: using-declaration causes a multiple declaration of 'boost::range::sort'  
  
In switched include order it produces:  
1>c:\views\conan\BoostHeaders\3.0.0+1.75.0\rs3\stable\package\5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9\include\boost/sort/pdqsort/pdqsort.hpp(29): error C2757: 'sort': a symbol with this name already exists and therefore this name cannot be used as a namespace name

---

## Comment 8

> Username: spreadsort  
> Created at: 2022-07-09 17:22:43 UTC  
> Url: https://github.com/boostorg/sort/issues/60#issuecomment-1179577272  

The second problem is because boost range doesn't follow the boost rules on namespaces.  It is a bug with boost range: https://github.com/boostorg/range/issues/126.  Feel free to comment there.
