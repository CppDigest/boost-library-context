# #34 Guard uses of std::max and std::min [Merged]

> Username: CaseyCarter  
> Created at: 2019-05-09 21:17:14 UTC  
> Updated at: 2019-05-21 11:55:24 UTC  
> Merged at: 2019-05-21 11:44:31 UTC  
> Closed at: 2019-05-21 11:44:31 UTC  
> Url: https://github.com/boostorg/sort/pull/34  

...in flat_stable_sort.hpp against the evils of windows.h.  
  
This is necessary to make `test_block_indirect_sort` compile correctly, since it includes `boost/test` headers - which include windows.h so it can print pretty colors on the console - before `boost/sort/sort.hpp`.

---

## Comment 1

> Username: fjtapia  
> Created_at: 2019-05-14 06:50:50 UTC  
> Url: https://github.com/boostorg/sort/pull/34#issuecomment-492102196  

Hi Casey,  
  
  
Thanks by your interest in the library. I examined your code, but don’t  
understand the need of put std::max and std::min in parentheses.  
  
And if we put these, why not all the others functions?  
  
I compiled with several versions of Visual Studio, GCC and CLANG, and  
didn’t have any error.  
  
You talk about problems with test_block_indirect_sort, but propose to make  
modifications in flat_stable_sort.  
  
Please, explain me the problem, because now, I don’t understand it.  
  
Thanks  
  
  
Francisco  
  
El jue., 9 may. 2019 a las 23:17, Casey Carter (<notifications@github.com>)  
escribió:  
  
> ...in flat_stable_sort.hpp against the evils of windows.h.  
>  
> This is necessary to make test_block_indirect_sort compile correctly,  
> since it includes boost/test headers - which include windows.h so it can  
> print pretty colors on the console - before boost/sort/sort.hpp.  
> ------------------------------  
> You can view, comment on, or merge this pull request online at:  
>  
>   https://github.com/boostorg/sort/pull/34  
> Commit Summary  
>  
>    - Guard uses of std::max and std::min  
>  
> File Changes  
>  
>    - *M* include/boost/sort/flat_stable_sort/flat_stable_sort.hpp  
>    <https://github.com/boostorg/sort/pull/34/files#diff-0> (4)  
>  
> Patch Links:  
>  
>    - https://github.com/boostorg/sort/pull/34.patch  
>    - https://github.com/boostorg/sort/pull/34.diff  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/pull/34>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GHNXFKYVMATTAFT7RLPUSIFVANCNFSM4HL6CUZQ>  
> .  
>

---

## Comment 2

> Username: ericniebler  
> Created_at: 2019-05-15 18:03:06 UTC  
> Url: https://github.com/boostorg/sort/pull/34#issuecomment-492760737  

> Thanks by your interest in the library. I examined your code, but don’t  
> understand the need of put std::max and std::min in parentheses.  
>  
> And if we put these, why not all the others functions?  
  
Windows.h infamously defines `min` and `max` as macros, causing code that uses `std::min` and `std::max` to fail to compile. Wrapping uses of `std::min` and `std::max` in parens is a common way to prevent those macros from expanding. If you don't believe me, search the rest of Boost. I'm willing to bet you'll find many more examples.

---

## Comment 3

> Username: fjtapia  
> Created_at: 2019-05-17 11:04:50 UTC  
> Url: https://github.com/boostorg/sort/pull/34#issuecomment-493415148  

Of course I believe you !  
  
  
I did not know the reason of the change.  
  
With your explanation, all is clear, and the modification is logic.  
  
Thanks  
  
  
Francisco  
  
El mié., 15 may. 2019 a las 20:03, Eric Niebler (<notifications@github.com>)  
escribió:  
  
> Thanks by your interest in the library. I examined your code, but don’t  
> understand the need of put std::max and std::min in parentheses.  
>  
> And if we put these, why not all the others functions?  
>  
> Windows.h infamously defines min and max as macros, causing code that  
> uses std::min and std::max to fail to compile. Wrapping uses of std::min  
> and std::max in parens is a common way to prevent those macros from  
> expanding. If you don't believe me, search the rest of Boost. I'm willing  
> to bet you'll find many more examples.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/pull/34?email_source=notifications&email_token=AA5O5GG3B3ZUUNLEKB5QX2DPVRF5VA5CNFSM4HL6CUZ2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODVPO5II#issuecomment-492760737>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GFF77SB2KWP2Z5KV3TPVRF5VANCNFSM4HL6CUZQ>  
> .  
>

---

## Comment 4

> Username: CaseyCarter  
> Created_at: 2019-05-17 17:43:08 UTC  
> Url: https://github.com/boostorg/sort/pull/34#issuecomment-493539154  

Sorry for the slow response here. We (MSVC) stumbled across this building Boost in our CI, and I've been trying to find some time to investigate why we see the issue and you do not. I'm concerned that our CI is building with a weird configuration, or with a peculiar combination of submodules. But yes, Eric is correct about the motivation: `Windows.h` defines `max` and `min` function-like macros (as well as the lesser-known `small` object-like macro), the boost unit test headers drag in `Windows.h`, and this particular test triggers nastiness by including the boost unit test headers *before* `boost/sort/sort.hpp` resulting in `min`s and `max`s in that header being macro-expanded.  
  
Changing the include order in the test would fix the issue, but I figure it's better to wrap the `min`s and `max`s in the actual product header to defend it against the possibility of users including `Windows.h`.

---

## Comment 5

> Username: fjtapia  
> Created_at: 2019-05-21 11:55:23 UTC  
> Url: https://github.com/boostorg/sort/pull/34#issuecomment-494359517  

Done.  
  
Thanks by your explanation. I like to learn  
  
  
El vie., 17 may. 2019 a las 19:43, Casey Carter (<notifications@github.com>)  
escribió:  
  
> Sorry for the slow response here. We (MSVC) stumbled across this building  
> Boost in our CI, and I've been trying to find some time to investigate why  
> we see the issue and you do not. I'm concerned that our CI is building with  
> a weird configuration, or with a peculiar combination of submodules. But  
> yes, Eric is correct about the motivation: Windows.h defines max and min  
> function-like macros (as well as the lesser-known small object-like  
> macro), the boost unit test headers drag in Windows.h, and this  
> particular test triggers nastiness by including the boost unit test headers  
> *before* boost/sort/sort.hpp resulting in mins and maxs in that header  
> being macro-expanded.  
>  
> Changing the include order in the test would fix the issue, but I figure  
> it's better to wrap the mins and maxs in the actual product header to  
> defend it against the possibility of users including Windows.h.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/pull/34?email_source=notifications&email_token=AA5O5GHE65W2RZG4JPVU7ADPV3VC3A5CNFSM4HL6CUZ2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODVVM6UQ#issuecomment-493539154>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDP4WIL5IUDX5T4UK3PV3VC3ANCNFSM4HL6CUZQ>  
> .  
>

---
