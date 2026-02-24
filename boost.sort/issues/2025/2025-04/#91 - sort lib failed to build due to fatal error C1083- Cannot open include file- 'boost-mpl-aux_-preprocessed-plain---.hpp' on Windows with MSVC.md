# #91 - sort lib failed to build due to fatal error C1083: Cannot open include file: 'boost/mpl/aux_/preprocessed/plain/||.hpp' on Windows with MSVC [Closed]

> Username: LinWrSpace  
> Created at: 2025-04-02 08:39:40 UTC  
> Updated at: 2025-09-06 17:47:22 UTC  
> Closed at: 2025-09-06 17:47:22 UTC  
> Url: https://github.com/boostorg/sort/issues/91  

**Issue Description:**  
Found Boost\libs\sort failed to build due to fatal error C1083: Cannot open include file: 'boost/mpl/aux_/preprocessed/plain/||.hpp' on Windows with MSVC, this issue can be reproduced on 6f50c3c commit. Could you please take a look? Thanks in advance.  
  
**Reproduce Steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git f:\Boost  
2. open a VS 2022 x64 command prompt and browse to f:\Boost  
3.  .\bootstrap  
4.  .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
5.  .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
6.  .\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\sort\test address-model=64  
  
[sort_test.log](https://github.com/user-attachments/files/19564574/sort_test.log)  
  
**ErrorMessage:**  
[1] msvc-14.3  
[2] msvc-14.3/release/x86_64/link-static/threadapi-win32/threading-multi/visibility-hidden  
[3] msvc-14.3/release/x86_64/threadapi-win32/threading-multi/visibility-hidden  
...patience...  
...found 2910 targets...  
...updating 101 targets...  
compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.3\release\x86_64\threading-multi\test_parallel_stable_sort.obj  
test_parallel_stable_sort.cpp  
.\boost/mpl/aux_/include_preprocessed.hpp(37): fatal error C1083: Cannot open include file: 'boost/mpl/aux_/preprocessed/plain/||.hpp': No such file or directory (C:\repos\msvc\src\vctools\Compiler\CxxFE\sl\p1\c\p0prepro.c:2141)  
warning C1511: could not create lock file: 'C:\output\Boost\preprocessed_repro_test\_lock'  
.....  
...failed updating 6 targets...  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\sort\test\test_block_indirect_sort.test\msvc-14.3\release\x86_64\threading-multi\test_block_indirect_sort.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\sort\test\test_flat_stable_sort.test\msvc-14.3\release\x86_64\threading-multi\test_flat_stable_sort.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\sort\test\test_insert_sort.test\msvc-14.3\release\x86_64\threading-multi\test_insert_sort.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.3\release\x86_64\threading-multi\test_parallel_stable_sort.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\sort\test\test_sample_sort.test\msvc-14.3\release\x86_64\threading-multi\test_sample_sort.obj  
   compile-c-c++ C:\gitP\boostorg\boost\amd64\boost\bin.v2\libs\sort\test\test_spinsort.test\msvc-14.3\release\x86_64\threading-multi\test_spinsort.obj

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-04-19 12:19:11 UTC  
> Url: https://github.com/boostorg/sort/issues/91#issuecomment-2816682135  

I had a similar issue migrating to boost 1.88.0 in recent weeks.  
This [patch](https://github.com/boostorg/mpl/pull/85) worked for us for VS 2019.

---

## Comment 2

> Username: fjtapia  
> Created at: 2025-04-19 15:39:34 UTC  
> Url: https://github.com/boostorg/sort/issues/91#issuecomment-2816756788  

Hello everyone,  
  
I've been investigating the error. At first glance, it seemed like  
something from the new version, because it worked correctly in previous  
versions.  
  
The problem is associated with BOOST_CHECK and its implementation with MPL. The  
error it gives is that the compilation cannot find a file named ||.hpp,  
which seems very strange.  
  
I spent several days trying to unravel MPL's path to that file, but it's a  
real maze due to the number of include files each one has.  
  
I then noticed that Steven's files compiled perfectly, and that mine were  
failing. Then a lightbulb went off, and I knew the cause of the problem. I  
use and, or, and not in my code, but for it to work in the Microsoft  
compiler, you have to include the ciso646 file (deprecated in GCC in  
C++17), as it doesn't directly support them like GCC and CLANG.  
  
I tried removing it from a program and changing the and, or, and not to &&,  
|| (like the name of the file not found), and !, and the program compiled  
without problems.  
  
Yesterday I changed it in all my files, but now I want to run all my tests,  
which are much more detailed and extensive than those in the library, and  
then update the repository.  
  
But as I said at the beginning, the problem is the MPL implementation of  
BOOST_CHECK for the Microsoft compiler.  
  
In all the years I've been working with the library, I've only had one  
minor problem due to my code. All the others are the fault and cause of the  
Microsoft compiler. When they're adults, like their older brothers GCC and  
CLANG, and less stupid, they'll understand that breaking the standard,  
unless you do something extraordinary, is idiotic, and it unnecessarily  
complicates the lives of those of us who make programs.  
  
Since it's Holy Week, I'll pray Rev.  Joseph Wall's prayer, *"My God, my  
God, deliver me from idiots, for I'll deliver myself from evil ones."*  
  
I'll start testing on Monday, and as soon as I'm done, I'll update the  
repository and send you a message.  
  
El sáb, 19 abr 2025 a las 14:19, Nigel Stewart ***@***.***>)  
escribió:  
  
> I had a similar issue migrating to boost 1.88.0 in recent weeks.  
> This patch <https://github.com/boostorg/mpl/pull/85> worked for us for VS  
> 2019.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/91#issuecomment-2816682135>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDHLWLEQMGK6NOKIBL22I5NJAVCNFSM6AAAAAB2I5VB66VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDQMJWGY4DEMJTGU>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
> *nigels-com* left a comment (boostorg/sort#91)  
> <https://github.com/boostorg/sort/issues/91#issuecomment-2816682135>  
>  
> I had a similar issue migrating to boost 1.88.0 in recent weeks.  
> This patch <https://github.com/boostorg/mpl/pull/85> worked for us for VS  
> 2019.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/91#issuecomment-2816682135>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDHLWLEQMGK6NOKIBL22I5NJAVCNFSM6AAAAAB2I5VB66VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDQMJWGY4DEMJTGU>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: fjtapia  
> Created at: 2025-04-29 07:01:42 UTC  
> Url: https://github.com/boostorg/sort/issues/91#issuecomment-2837703248  

Done  
  
El sáb, 19 abr 2025 a las 17:39, Francisco José Tapia ***@***.***>)  
escribió:  
  
> Hello everyone,  
>  
> I've been investigating the error. At first glance, it seemed like  
> something from the new version, because it worked correctly in previous  
> versions.  
>  
> The problem is associated with BOOST_CHECK and its implementation with MPL. The  
> error it gives is that the compilation cannot find a file named ||.hpp,  
> which seems very strange.  
>  
> I spent several days trying to unravel MPL's path to that file, but it's a  
> real maze due to the number of include files each one has.  
>  
> I then noticed that Steven's files compiled perfectly, and that mine were  
> failing. Then a lightbulb went off, and I knew the cause of the problem. I  
> use and, or, and not in my code, but for it to work in the Microsoft  
> compiler, you have to include the ciso646 file (deprecated in GCC in  
> C++17), as it doesn't directly support them like GCC and CLANG.  
>  
> I tried removing it from a program and changing the and, or, and not to  
> &&, || (like the name of the file not found), and !, and the program  
> compiled without problems.  
>  
> Yesterday I changed it in all my files, but now I want to run all my  
> tests, which are much more detailed and extensive than those in the  
> library, and then update the repository.  
>  
> But as I said at the beginning, the problem is the MPL implementation of  
> BOOST_CHECK for the Microsoft compiler.  
>  
> In all the years I've been working with the library, I've only had one  
> minor problem due to my code. All the others are the fault and cause of  
> the Microsoft compiler. When they're adults, like their older brothers  
> GCC and CLANG, and less stupid, they'll understand that breaking the  
> standard, unless you do something extraordinary, is idiotic, and it  
> unnecessarily complicates the lives of those of us who make programs.  
>  
> Since it's Holy Week, I'll pray Rev.  Joseph Wall's prayer, *"My God, my  
> God, deliver me from idiots, for I'll deliver myself from evil ones."*  
>  
> I'll start testing on Monday, and as soon as I'm done, I'll update the  
> repository and send you a message.  
>  
> El sáb, 19 abr 2025 a las 14:19, Nigel Stewart ***@***.***>)  
> escribió:  
>  
>> I had a similar issue migrating to boost 1.88.0 in recent weeks.  
>> This patch <https://github.com/boostorg/mpl/pull/85> worked for us for  
>> VS 2019.  
>>  
>> —  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/sort/issues/91#issuecomment-2816682135>, or  
>> unsubscribe  
>> <https://github.com/notifications/unsubscribe-auth/AA5O5GDHLWLEQMGK6NOKIBL22I5NJAVCNFSM6AAAAAB2I5VB66VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDQMJWGY4DEMJTGU>  
>> .  
>> You are receiving this because you were assigned.Message ID:  
>> ***@***.***>  
>> *nigels-com* left a comment (boostorg/sort#91)  
>> <https://github.com/boostorg/sort/issues/91#issuecomment-2816682135>  
>>  
>> I had a similar issue migrating to boost 1.88.0 in recent weeks.  
>> This patch <https://github.com/boostorg/mpl/pull/85> worked for us for  
>> VS 2019.  
>>  
>> —  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/sort/issues/91#issuecomment-2816682135>, or  
>> unsubscribe  
>> <https://github.com/notifications/unsubscribe-auth/AA5O5GDHLWLEQMGK6NOKIBL22I5NJAVCNFSM6AAAAAB2I5VB66VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDQMJWGY4DEMJTGU>  
>> .  
>> You are receiving this because you were assigned.Message ID:  
>> ***@***.***>  
>>  
>

---

## Comment 4

> Username: nigels-com  
> Created at: 2025-04-29 08:25:42 UTC  
> Url: https://github.com/boostorg/sort/issues/91#issuecomment-2837919651  

Thanks Francisco, much appreciated change.

---

## Comment 5

> Username: ThirtyNineWinks  
> Created at: 2025-06-09 13:10:26 UTC  
> Url: https://github.com/boostorg/sort/issues/91#issuecomment-2955752502  

Including `ciso646` is/was done by many things, including `gsl` and `absl`. If we include anything that includes them before we include anything that includes `mpl` (like `variant`) we get compilation errors. The workaround is possible (force including mpl early), but it is ugly.

---

## Comment 6

> Username: nigels-com  
> Created at: 2025-08-31 03:57:21 UTC  
> Url: https://github.com/boostorg/sort/issues/91#issuecomment-3239734910  

Likely fixed in [boost 1.89.0](https://www.boost.org/releases/1.89.0/), can anyone confirm?

---

## Comment 7

> Username: fjtapia  
> Created at: 2025-09-02 08:23:11 UTC  
> Url: https://github.com/boostorg/sort/issues/91#issuecomment-3244314933  

yes. It's resolved  
  
El dom, 31 ago 2025 a las 5:57, Nigel Stewart ***@***.***>)  
escribió:  
  
> *nigels-com* left a comment (boostorg/sort#91)  
> <https://github.com/boostorg/sort/issues/91#issuecomment-3239734910>  
>  
> Likely fixed in boost 1.89.0 <https://www.boost.org/releases/1.89.0/>,  
> can anyone confirm?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/91#issuecomment-3239734910>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDBQTA4FEH4DJ4RMBD3QJXDPAVCNFSM6AAAAAB2I5VB66VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZTEMZZG4ZTIOJRGA>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>
