# #24 - Add a complimentary boost::unique_any [Closed]

> Username: gloinart  
> Created at: 2021-09-21 19:52:55 UTC  
> Updated at: 2023-09-05 21:12:41 UTC  
> Closed at: 2023-05-14 17:16:07 UTC  
> Url: https://github.com/boostorg/any/issues/24  

Add a variant of boost::any which accepts move-only object such as std::unique_ptr (and therefore, the boost::any is move-only itself)

---

## Comment 1

> Username: apolukhin  
> Created at: 2023-04-29 19:24:40 UTC  
> Url: https://github.com/boostorg/any/issues/24#issuecomment-1528855567  

It is implemented for quite some time https://github.com/boostorg/any/blob/develop/include/boost/any/unique_any.hpp  
  
However, I had no time to write tests and update docs on it. PR are welcome

---

## Comment 2

> Username: apolukhin  
> Created at: 2023-05-14 17:16:07 UTC  
> Url: https://github.com/boostorg/any/issues/24#issuecomment-1546950983  

Looks like done  
  
Should be shipped in Boost 1.83

---

## Comment 3

> Username: gloinart  
> Created at: 2023-05-14 18:50:55 UTC  
> Url: https://github.com/boostorg/any/issues/24#issuecomment-1546974777  

Hi Antony,  
  
Sounds great, looking forward to using it!  
  
Best/Viktor  
  
On Sun, May 14, 2023 at 7:16 PM Antony Polukhin ***@***.***>  
wrote:  
  
> Looks like done  
>  
> Should be shipped in Boost 1.83  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/any/issues/24#issuecomment-1546950983>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAJLEQAYNT6OJNXIPD2VUL3XGEHODANCNFSM5EPQ2OJQ>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 4

> Username: gloinart  
> Created at: 2023-09-05 21:12:41 UTC  
> Url: https://github.com/boostorg/any/issues/24#issuecomment-1707317030  

Hi,  
  
A note on unique_any. In difference to boost::any it seems to miss the  
"empty()" member function and contains "has_value()" instead.  
Although this rhymes with the implementation of std::any, wouldn't it be an  
idea to synchronize the interface for all the anys in boost::any?  
(i.e both have has_value(), empty(), and emplace())  
  
Best/Viktor  
  
  
On Sun, May 14, 2023 at 8:50 PM Viktor Sehr ***@***.***> wrote:  
  
> Hi Antony,  
>  
> Sounds great, looking forward to using it!  
>  
> Best/Viktor  
>  
> On Sun, May 14, 2023 at 7:16 PM Antony Polukhin ***@***.***>  
> wrote:  
>  
>> Looks like done  
>>  
>> Should be shipped in Boost 1.83  
>>  
>> —  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/any/issues/24#issuecomment-1546950983>, or  
>> unsubscribe  
>> <https://github.com/notifications/unsubscribe-auth/AAJLEQAYNT6OJNXIPD2VUL3XGEHODANCNFSM5EPQ2OJQ>  
>> .  
>> You are receiving this because you authored the thread.Message ID:  
>> ***@***.***>  
>>  
>
