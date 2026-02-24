# #64 - boost/sort/common/util/insert.hpp #includes itself [Closed]

> Username: urnathan  
> Created at: 2022-08-29 18:59:03 UTC  
> Updated at: 2023-09-18 22:57:11 UTC  
> Closed at: 2023-09-18 22:57:11 UTC  
> Url: https://github.com/boostorg/sort/issues/64  

working on clang modules (as a proxy for C++20 header-units), I see that boost/sort/common/util/insert.hpp #includes itself.  That seems unnatural.  
  
[boost-includes.txt](https://github.com/boostorg/sort/files/9447177/boost-includes.txt)

---

## Comment 1

> Username: nigels-com  
> Created at: 2023-09-11 21:30:45 UTC  
> Url: https://github.com/boostorg/sort/issues/64#issuecomment-1714612180  

Resolved via PR #78, can be closed now.

---

## Comment 2

> Username: fjtapia  
> Created at: 2023-09-13 16:10:51 UTC  
> Url: https://github.com/boostorg/sort/issues/64#issuecomment-1717927921  

The insert sort algorithm is in the file insert_sort.hpp.  
This file has an include insert.hpp, which contains quick insert functions  
in an ordered vector. DOES NOT INCLUDE HIMSELF  
  
These functions were initially part of the insert_sort algorithm. But they  
were later implemented in the insert sort.hpp file, so right now both the  
include and the insert.hpp files are unnecessary.  
The file test_insert_sort.cpp also tested those functions, and they part  
were deleted from the file  
  
They are remains of an old implementation, which I forgot to clean when I  
uploaded the files to the repository, as well as a function placed between  
comments in the file.  
I had modified insert_sort.hpp, delete insert.hpp, and modified, too,  
test_insert_sort  
  
By the way, what program did you use to detect this?  
Thanks  
  
Francisco  
  
El lun, 11 sept 2023 a las 23:30, Nigel Stewart ***@***.***>)  
escribió:  
  
> Resolved via PR #78 <https://github.com/boostorg/sort/pull/78>, can be  
> closed now.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/64#issuecomment-1714612180>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GGBXEA6OWELBP4KDXLXZ57I7ANCNFSM5763OKJA>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: urnathan  
> Created at: 2023-09-13 23:01:49 UTC  
> Url: https://github.com/boostorg/sort/issues/64#issuecomment-1718420521  

> By the way, what program did you use to detect this?  
  
I probably hacked the compiler to avoid its header-macro optimization and then ran some python of the preprocessed output.  FWIW I've been wondering about the usefulness of a compiler warning for this kind of thing.
