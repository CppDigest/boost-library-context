# #21 12258: re-enable unit test for random_device generator [Merged]

> Username: jeking3  
> Created at: 2017-08-10 16:07:31 UTC  
> Updated at: 2018-07-04 17:59:59 UTC  
> Merged at: 2017-08-10 16:50:07 UTC  
> Closed at: 2017-08-10 16:50:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/21  

Investigating Trac 12258 I found the random_device unit test was not enabled.  This establishes a formal dependency during the build of uuid on random, but that seems quite appropriate.  Tested on Windows (MSVC2015) and Linux (gcc-4.8, trusty).

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-08-10 16:36:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/21#issuecomment-321605623  

Did you check the history for test_random_generator.cpp to see why it was disabled ? I agree with you that it should not have been disabled simply because the test establishes a dependency on another Boost library.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-08-10 16:45:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/21#issuecomment-321608279  

A fine idea, I will do that!  
  
On Thu, Aug 10, 2017 at 12:36 PM, Edward Diener <notifications@github.com>  
wrote:  
  
> Did you check the history for test_random_generator.cpp to see why it was  
> disabled ? I agree with you that it should not have been disabled simply  
> because the test establishes a dependency on another Boost library.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/uuid/pull/21#issuecomment-321605623>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbVuWp6WVfyOgqrGu_NF5S7jd8Jxxks5sWzF0gaJpZM4Ozpel>  
> .  
>

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-08-10 16:47:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/21#issuecomment-321608596  

It was always disabled, as far back as 8 years ago.  No history on why it  
was commented out.  
  
On Thu, Aug 10, 2017 at 12:45 PM, James King <jeking3rd@gmail.com> wrote:  
  
> A fine idea, I will do that!  
>  
> On Thu, Aug 10, 2017 at 12:36 PM, Edward Diener <notifications@github.com>  
> wrote:  
>  
>> Did you check the history for test_random_generator.cpp to see why it was  
>> disabled ? I agree with you that it should not have been disabled simply  
>> because the test establishes a dependency on another Boost library.  
>>  
>> —  
>> You are receiving this because you authored the thread.  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/uuid/pull/21#issuecomment-321605623>, or mute  
>> the thread  
>> <https://github.com/notifications/unsubscribe-auth/ALOdbVuWp6WVfyOgqrGu_NF5S7jd8Jxxks5sWzF0gaJpZM4Ozpel>  
>> .  
>>  
>  
>

---
