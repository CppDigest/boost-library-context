# #45 - spinsort and flat_stable_sort don't compile with -fno-exceptions or -fno-operator-names GCC flags [Open]

> Username: vedgy  
> Created at: 2020-11-02 09:29:48 UTC  
> Updated at: 2024-03-01 18:53:00 UTC  
> Url: https://github.com/boostorg/sort/issues/45  

Boost version: 1.72.0 (Arch Linux package version: *boost 1.72.0-2*).  
  
The other two sorting methods I tested - `spreadsort::float_sort` and `pdqsort` - work just fine with these compiler flags.  
  
These two flags are used by default in KDE: https://invent.kde.org/frameworks/extra-cmake-modules/-/blob/master/kde-modules/KDECompilerSettings.cmake. Possible workaround (which will hopefully work): create a tiny static library that disables these flags and calls `spinsort` or `flat_stable_sort`.

---

## Comment 1

> Username: fjtapia  
> Created at: 2020-11-03 17:22:36 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-721268253  

An empty range to sort, it is not a problem. Is the same as with 1 element.  
Do nothing  
  
The flat_stable_sort and spin_sort need additional memory. And when fail  
it, throw a bad_alloc exception.  
It would be possible to do without exceptions. I need to detect in the  
compile-time and abort the program.  
It has logic. I will do it, but I will need time because now I am very busy.  
Thanks for your interest  
  
Francisco  
  
El lun., 2 nov. 2020 a las 10:30, Igor Kushnir (<notifications@github.com>)  
escribió:  
  
> Boost version: 1.72.0 (Arch Linux package version: *boost 1.72.0-2*).  
>  
> The other two sorting methods I tested - spreadsort::float_sort and  
> pdqsort - work just fine with these compiler flags.  
>  
> These two flags are used by default in KDE:  
> https://invent.kde.org/frameworks/extra-cmake-modules/-/blob/master/kde-modules/KDECompilerSettings.cmake.  
> Possible workaround (which will hopefully work): create a tiny static  
> library that disables these flags and calls spinsort or flat_stable_sort.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/45>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDN3TD5DWOWDT3XBY3SNZ3ZXANCNFSM4THHIPYQ>  
> .  
>

---

## Comment 2

> Username: shehab-ashraf  
> Created at: 2021-01-13 02:53:06 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-759169804  

Hey @fjtapia ,  
are you willing to accept new contributors? I am interested into contributing to this repository so, if you could explain a bit more this feature request, it would be great.  
Thanks

---

## Comment 3

> Username: nigels-com  
> Created at: 2024-01-26 23:51:03 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1912839991  

I was doing some performance characterisation work recently.  I had some difficulty building in gcc `--no-exceptions` mode due to `boost::block_indirect_sort`.  The workaround for me was to simply comment out the offending try, catch and throws.  
  
But I did also happen across `BOOST_NO_EXCEPTIONS` and [Boost.ThrowException](https://www.boost.org/doc/libs/master/libs/throw_exception/doc/html/throw_exception.html) which does seem intended as a way of supporting _both_ with and without exception handling.  
  
There are only 20 or so places in boost::sort throwing exceptions, so seems quite doable to make a change to support  `BOOST_NO_EXCEPTIONS` mode.  The performance improvement in my testing amounted to about 2% overall, which isn't so compelling on the face of it.  But it does seem like it might be more significant for a different code path and we might like to use `--no-exceptions` mode, and it would be nice for boost::sort to support that.  
  
I don't think that boost::sort performance would be significantly improved with `--no-exceptions` directly.  But if the rest of the codebase is in C, or not especially exception-happy C++, it could make sense.  The other example I came across is [spdlog](https://github.com/gabime/spdlog) [SPDLOG_NO_EXCEPTIONS](https://github.com/gabime/spdlog/blob/v2.x/include/spdlog/common.h#L58).

---

## Comment 4

> Username: fjtapia  
> Created at: 2024-02-01 14:08:41 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1921414235  

I am working on it. In a short time I will upload the corrections.  
Thanks  
  
Francisco Tapia  
***@***.***  
  
El sáb, 27 ene 2024 a las 0:51, Nigel Stewart ***@***.***>)  
escribió:  
  
> I was doing some performance characterisation work recently. I had some  
> difficulty building in gcc --no-exceptions mode due to  
> boost::block_indirect_sort. The workaround for me was to simply comment  
> out the offending try, catch and throws.  
>  
> But I did also happen across BOOST_NO_EXCEPTIONS and Boost.ThrowException  
> <https://www.boost.org/doc/libs/master/libs/throw_exception/doc/html/throw_exception.html>  
> which does seem intended as a way of supporting *both* with and without  
> exception handling.  
>  
> There are only 20 or so places in boost::sort throwing exceptions, so  
> seems quite doable to make a change to support BOOST_NO_EXCEPTIONS mode.  
> The performance improvement in my testing amounted to about 2% overall,  
> which isn't so compelling on the face of it. But it does seem like it might  
> be more significant for a different code path and we might like to use  
> --no-exceptions mode, and it would be nice for boost::sort to support  
> that.  
>  
> I don't think that boost::sort performance would be significantly improved  
> with --no-exceptions directly. But if the rest of the codebase is in C,  
> or not especially exception-happy C++, it could make sense. The other  
> example I came across is spdlog <https://github.com/gabime/spdlog>  
> SPDLOG_NO_EXCEPTIONS  
> <https://github.com/gabime/spdlog/blob/v2.x/include/spdlog/common.h#L58>.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/45#issuecomment-1912839991>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GHON43UNKOD5NAAUKTYQQ6PDAVCNFSM4THHIPY2U5DIOJSWCZC7NNSXTN2JONZXKZKDN5WW2ZLOOQ5TCOJRGI4DGOJZHEYQ>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>

---

## Comment 5

> Username: fjtapia  
> Created at: 2024-02-29 16:26:23 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1971501383  

Hello Igor,  
  
I have already changed the Boost Sort code so that it can compile with  
-fno-exception and -fno-operator-names.  
  
I read in your message that KDE uses it. The utility of not using  
exceptions is clear to me, but I don't understand the utility of the  
-fno-operator-names option. If someone could explain it to me, I would  
appreciate it.  
  
I think that using and or and not in logical expressions brings clarity and  
makes the code easier to read and understand. Moreover, GCC and CLANG  
support it natively according to the C++ standard.  
( https://en.cppreference.com/w/cpp/language/operator_alternative )  
Visual C++ does not support it and to use it you have to include the iso646  
file, which in GCC and CLANG is an empty file. But it allows you to use it.  
  
Yours  
Francisco Tapia  
***@***.***  
  
El lun, 2 nov 2020 a las 10:30, Igor Kushnir ***@***.***>)  
escribió:  
  
> Boost version: 1.72.0 (Arch Linux package version: *boost 1.72.0-2*).  
>  
> The other two sorting methods I tested - spreadsort::float_sort and  
> pdqsort - work just fine with these compiler flags.  
>  
> These two flags are used by default in KDE:  
> https://invent.kde.org/frameworks/extra-cmake-modules/-/blob/master/kde-modules/KDECompilerSettings.cmake.  
> Possible workaround (which will hopefully work): create a tiny static  
> library that disables these flags and calls spinsort or flat_stable_sort.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/45>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDN3TD5DWOWDT3XBY3SNZ3ZXANCNFSM4THHIPYQ>  
> .  
>

---

## Comment 6

> Username: vedgy  
> Created at: 2024-02-29 17:50:21 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1971654801  

Hi Francisco,  
  
I am not a proponent or advocate of this flag myself. I had tried to use `flat_stable_sort()` to speed up opening a project in KDevelop, found the missing support a significant obstacle, and so logged this bug. Eventually I had found and switched to [`timsort()`](https://github.com/timsort/cpp-TimSort), because it turned out to be much faster than `flat_stable_sort()` for the KDevelop's use case (`flat_stable_sort()` had been a strong second-fast sorting algorithm for the use case).  
  
I just searched and found the original proposal and discussion of `-fno-operator-names` in KDE: https://phabricator.kde.org/D3850. I think the discussion can be summarized like this:  
* the flags contradict the C++ standard, so enabling them by default in KDE code is not universally agreed on;  
* there is a practical portability benefit (not breaking a MSVC build) of using this flag by default to compile KDE code, because not all KDE projects are regularly compiled on Windows;  
* the flag can be overridden for [sub]projects that depend on 3rd-party libraries/headers, which do not compile with it.

---

## Comment 7

> Username: fjtapia  
> Created at: 2024-02-29 20:16:32 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1971885809  

Hello Igor,  
  
thanks for your answer. The problem comes, as far as I see, from Visual  
C++. As I told you in the previous message, the solution is to put the  
#include <iso646> and with  MSVC++ works and with GCC and CLANG too, since  
in both it is an empty file.  
  
But, if it is useful, to do without them, for the reasons you have told me,  
we will do without. It is not a problem. I was just curious to know. Thanks  
for the explanation.  
  
Regarding the discussion about flat_stable_sort and pdqsort, they are  
different things. A spoon and fork are both for eating, but they are  
different and each is used for one thing. Pdqsort is not stable, and  
flat_stable_sort is. The algorithm stability thing is very easy to say, but  
it imposes very significant costs on algorithm design and speed.  
  
Non-stable algorithms, such as pdqsort use a filtering algorithm by a value  
and make smaller and smaller parts, which are then sorted by insert_sort.  
These algorithms are extremely efficients  
  
Stable algorithms have to use a mixing algorithm because the filtering is  
not stable. They make very small parts, which are sorted, and then merged  
by making larger and larger parts until they reach the size of the data to  
be sorted.  When mixing, they use an auxiliary memory, which is usually  
half the size of the data.  
  
flat_stable_sort is a new stable algorithm, which internally uses  
fixed-length blocks, and only needs as auxiliary memory, one fixed-length  
block and an index to know the correct order of the blocks. Such auxiliary  
memory is usually less than 0.1% of the data memory.  
However, handling the blocks comes at a cost in complexity and time. Due  
this flat_stable_sort is slower than other stable algorithms such as  
stable_sort or spin_sort.  It has about 80% of the speed of the other  
algorithms. But their additional memory is very small compared to them. The  
algorithm is very efficient, too, when the data are near sorted, as  
unsorted elements added to the beginning or the end of the sorted elements  
  
You have a piece of clear information in the benchmarks section of the  
project documentation.  
https://www.boost.org/doc/libs/1_84_0/libs/sort/doc/html/sort/single_thread/linux_single.html  
  
If I can help you in any way, or if KDE needs help, please contact me  
Yours  
  
Francisco  
  
El jue, 29 feb 2024 a las 18:50, Igor Kushnir ***@***.***>)  
escribió:  
  
> Hi Francisco,  
>  
> I am not a proponent or advocate of this flag myself. I had tried to use  
> flat_stable_sort() to speed up opening a project in KDevelop, found the  
> missing support a significant obstacle, and so logged this bug. Eventually  
> I had found and switched to timsort()  
> <https://github.com/timsort/cpp-TimSort>, because it turned out to be  
> much faster than flat_stable_sort() for the KDevelop's use case (  
> flat_stable_sort() had been a strong second-fast sorting algorithm for  
> the use case).  
>  
> I just searched and found the original proposal and discussion of  
> -fno-operator-names in KDE: https://phabricator.kde.org/D3850. I think  
> the discussion can be summarized like this:  
>  
>    - the flags contradict the C++ standard, so enabling them by default  
>    in KDE code is not universally agreed on;  
>    - there is a practical portability benefit (not breaking a MSVC build)  
>    of using this flag by default to compile KDE code, because not all KDE  
>    projects are regularly compiled on Windows;  
>    - the flag can be overridden for [sub]projects that depend on  
>    3rd-party libraries/headers, which do not compile with it.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/45#issuecomment-1971654801>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GE3IOOGDCEQOAGDVNLYV5U6TAVCNFSM4THHIPY2U5DIOJSWCZC7NNSXTN2JONZXKZKDN5WW2ZLOOQ5TCOJXGE3DKNBYGAYQ>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>

---

## Comment 8

> Username: vedgy  
> Created at: 2024-03-01 11:04:52 UTC  
> Updated at: 2024-03-01 11:11:18 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1972985414  

In the KDevelop use case, the elements are relatively expensive to compare and move, and indeed are mostly sorted from the start. So `flat_stable_sort()` handily outperforms `std::stable_sort()`, `std::sort()`, `boost::sort::spinsort()` and every other sorting algorithm I have tried except for `timsort()`, which is even more fine-tuned for this specific task.  
  
Thanks for making `flat_stable_sort()` compile with `-fno-exceptions` and `-fno-operator-names`. Now KDE projects can use it without hindrance.  
  
EDIT: reopening the issue because `boost::sort::spinsort()` does not seem to compile with these flags yet.

---

## Comment 9

> Username: nigels-com  
> Created at: 2024-03-01 12:30:07 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1973106333  

I think there is fairly strong argument to avoid operator names.  
  
At our organisation they are strongly discouraged.  The compiler flags are set accordingly.  
Having to manage that for dependencies with other opinions is simply work that needs doing, of no functional or practical value.  
  
Ease of integration with other code bases is a positive, even if it isn't as we'd personally prefer it to be.  
Minimise the friction.

---

## Comment 10

> Username: fjtapia  
> Created at: 2024-03-01 14:10:45 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1973274380  

Hi Igor,  
  
in the algorithms I have invented (spin_sort, flat_stable_sort, and  
block_indirect_sort) The algorithms adapt their internal parameters  
depending on the size of the data to be sorted, and the number of data.  
  
I am rewriting the 5 algorithms I am responsible for to make them faster  
and reduce the auxiliary memory consumption (in preliminary tests the new  
version of spinsort is faster than Timsort in all cases, except with  
strings, which is similar).  
I am also preparing 3 new algorithms, one single thread and two parallel,  
especially a stable parallel one, based on fixed-length blocks, whose  
additional memory is a block and the index to know the order of the blocks.  
If the results are what they promise, I will propose them for inclusion in  
the library.  
  
My problem is that  
I am a man alone,  
designing solutions to problems that I don't have,  
running on machines that I don't know,  
and whose results I will never see.  
My environment is my chair, my table, my old computer, and everything else  
is in my imagination. How to adapt an algorithm to be efficient on a  
microcontroller with little memory, on a server with 2 TB of memory and 128  
threads, and even on a supercomputer with thousands of machines.  
  
If you could provide me with some information about the type of data to  
sort, the size of the data, the number of elements to sort, and approximate  
times, I would appreciate it, because that allows me to see where my  
imagination does not reach and that helps me to improve the algorithms,  
good for you and good for me.  
Thanks for your interest  
Francisco  
  
El vie, 1 mar 2024 a las 13:30, Nigel Stewart ***@***.***>)  
escribió:  
  
> I think there is fairly strong argument to avoid operator names.  
>  
> At our organisation they are strongly discouraged. The compiler flags are  
> set accordingly.  
> Having to manage that for dependencies with other opinions is simply work  
> that needs doing, of no functional or practical value.  
>  
> Ease of integration with other code bases is a positive, even if it isn't  
> as we'd personally prefer it to be.  
> Minimise the friction.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/45#issuecomment-1973106333>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GDJGC4RXKGLA3H6AHTYWBYFXAVCNFSM4THHIPY2U5DIOJSWCZC7NNSXTN2JONZXKZKDN5WW2ZLOOQ5TCOJXGMYTANRTGMZQ>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>

---

## Comment 11

> Username: vedgy  
> Created at: 2024-03-01 18:49:03 UTC  
> Updated at: 2024-03-01 18:50:42 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1973743185  

> If you could provide me with some information about the type of data to  
sort, the size of the data, the number of elements to sort, and approximate  
times, I would appreciate it  
  
First of all, `timsort()` works fast enough that further optimization is not very important to me. So I am not asking you to optimize for my use case :)  
  
I have already described general characteristics of the sorting task. More specifically, the element is a struct that contains: 1) two copy-on-write `QVector<QString>`s with a custom optimized comparison; 2) an expensive to move/copy but cheap to compare custom KDevelop type. The initial order consists of usually long runs of already sorted elements: paths obtained from a file system API, then inserted into a file-system tree, then appended to a `QVector` (a Qt class similar to `std::vector`) via depth-first-search of the tree.  
  
If you need more details, here is the optimizing commit: https://commits.kde.org/kdevelop/09426bcff8060b1b6f1bb450c2ce6595861f3c0f. There are automated KDevelop benchmarks that are affected by the choice of sorting algorithm. But the main criterion of my sorting algorithm selection was manual benchmarking: opening large projects in KDevelop and comparing intrusive `QElapsedTimer` measurements of the sorting call.

---

## Comment 12

> Username: fjtapia  
> Created at: 2024-03-01 18:52:59 UTC  
> Url: https://github.com/boostorg/sort/issues/45#issuecomment-1973748129  

Thanks a lot  
  
Francisco  
  
El vie, 1 mar 2024 a las 19:49, Igor Kushnir ***@***.***>)  
escribió:  
  
> If you could provide me with some information about the type of data to  
> sort, the size of the data, the number of elements to sort, and approximate  
> times, I would appreciate it  
>  
> First of all, timsort() works fast enough that further optimization is  
> not very important to me. So I am not asking you to optimize for my use  
> case :)  
>  
> I have already described general characteristics of the sorting task. More  
> specifically, the element is a struct that contains: 1) two copy-on-write  
> QVector<QString>s with a custom optimized comparison; 2) an expensive to  
> move/copy but cheap to compare custom KDevelop type. The initial order  
> consists of usually long runs of already sorted elements: paths obtained  
> from a file system API, then inserted into a file-system tree, then  
> inserted into a QVector (a Qt class similar to std::vector) via  
> depth-first-search of the tree.  
>  
> If you need more details, here is the optimizing commit:  
> https://commits.kde.org/kdevelop/09426bcff8060b1b6f1bb450c2ce6595861f3c0f.  
> There are automated KDevelop benchmarks that are affected by the choice of  
> sorting algorithm. But the main criterion of my sorting algorithm selection  
> was manual benchmarking: opening large projects in KDevelop and comparing  
> intrusive QElapsedTimer measurements of the sorting call.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/45#issuecomment-1973743185>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GGMFJCQRZPJMIL4QK3YWDESXAVCNFSM4THHIPY2U5DIOJSWCZC7NNSXTN2JONZXKZKDN5WW2ZLOOQ5TCOJXGM3TIMZRHA2Q>  
> .  
> You are receiving this because you were assigned.Message ID:  
> ***@***.***>  
>
