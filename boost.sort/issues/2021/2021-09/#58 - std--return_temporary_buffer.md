# #58 - std::return_temporary_buffer [Open]

> Username: Remotion  
> Created at: 2021-09-30 19:57:45 UTC  
> Updated at: 2023-09-20 11:21:12 UTC  
> Url: https://github.com/boostorg/sort/issues/58  

[std::get_temporary_buffer](https://en.cppreference.com/w/cpp/memory/get_temporary_buffer) and [std::return_temporary_buffer](https://en.cppreference.com/w/cpp/memory/return_temporary_buffer) that are used in this library are removed in c++20!

---

## Comment 1

> Username: fjtapia  
> Created at: 2021-10-01 06:41:16 UTC  
> Url: https://github.com/boostorg/sort/issues/58#issuecomment-931952516  

Hi,  
thanks for your message.  
I am writing a new version of the algorithms, to make them faster, and to  
add two new parallel algorithms that I ideate. In this new version, the  
mentioned problem is resolved.  
  
But, unfortunately, I am delayed according to my initial planning. I think  
it will be better to modify the actual version and be ready to use with  
C++20 until the new version is ready.  
Thanks again  
  
Francisco  
  
El jue, 30 sept 2021 a las 21:57, Remo ***@***.***>)  
escribió:  
  
> https://en.cppreference.com/w/cpp/memory/return_temporary_buffer that is  
> used in this library was removed in c++20  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/58>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GHMEC7GGMTGCJACO3LUES6MHANCNFSM5FDDMBLQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>

---

## Comment 2

> Username: nigels-com  
> Created at: 2023-09-20 11:21:12 UTC  
> Url: https://github.com/boostorg/sort/issues/58#issuecomment-1727528026  

It appears this was resolved after boost-1.81.0 and before boost-1.82.0 tag.  
  
```  
$ git reset --hard boost-1.81.0  
HEAD is now at 72a3ae8 Merge pull request #57 from boostorg/develop  
  
$ rgrep get_temporary_buffer .  
./include/boost/sort/common/util/circular_buffer.hpp:        ptr = std::get_temporary_buffer < Value_t > (NMAX).first;  
./include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:        ptr = std::get_temporary_buffer<value_t>(Block_size * nthread).first;  
./include/boost/sort/parallel_stable_sort/parallel_stable_sort.hpp:    ptr = std::get_temporary_buffer<value_t>(nptr).first;  
./include/boost/sort/sample_sort/sample_sort.hpp:        value_t *ptr = std::get_temporary_buffer<value_t>(nelem).first;  
./include/boost/sort/spinsort/spinsort.hpp:        ptr = std::get_temporary_buffer<value_t>(nptr).first;  
  
$ git reset --hard boost-1.82.0  
HEAD is now at f2a2396 Merge pull request #67 from boostorg/develop  
$ rgrep get_temporary_buffer .  
$ rgrep return_temporary_buffer .  
```  
  
I think this issue can be closed as fixed.
