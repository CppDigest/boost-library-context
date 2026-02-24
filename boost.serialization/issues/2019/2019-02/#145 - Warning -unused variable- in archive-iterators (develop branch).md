# #145 - Warning "unused variable" in archive/iterators (develop branch) [Closed]

> Username: HDembinski  
> Created at: 2019-02-14 09:04:00 UTC  
> Updated at: 2022-11-15 18:05:35 UTC  
> Closed at: 2019-03-20 07:04:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/145  

Could you please remove this?  
./boost/archive/iterators/wchar_from_mb.hpp:168:31: warning: unused variable ‘r’ [-Wunused-variable]

---

## Comment 1

> Username: robertramey  
> Created at: 2019-02-24 18:34:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-466803176  

Hmm - if I do that, the subsequent BOOST_ASSERT wouldn't work

---

## Comment 2

> Username: alfC  
> Created at: 2019-02-24 18:58:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-466805324  

I think you can do `(void)var;` to suppress the warning. It will be clear  
it is related to the (sometimes compiled) assert.  
  
On Sun, Feb 24, 2019, 10:34 Robert Ramey <notifications@github.com> wrote:  
  
> Hmm - if I do that, the subsequent BOOST_ASSERT wouldn't work  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/145#issuecomment-466803176>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYFQwvBJOxfTFE851HziK5a0BkP_Hks5vQts_gaJpZM4a7IQ4>  
> .  
>

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-02-24 22:33:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-466824604  

@alfC is correct.

---

## Comment 4

> Username: robertramey  
> Created at: 2019-02-25 04:53:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-466871793  

> I think you can do `(void)var;` to suppress the warning. It will be clear it is related to the (sometimes compiled) assert.  
  
Hmmm - could you expand upon that?  You need to be more explicit so I can just paste it in.

---

## Comment 5

> Username: alfC  
> Created at: 2019-02-25 05:33:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-466877644  

Sorry, this is what I mean:  
  
In general, if you have a variable that is only used sometimes (depending  
on macro definitions) you can fake its usage with `(void)`.  
I am not familiar with this part of your code, but I assume that you can do  
this:  
  
```  
    std::codecvt_base::result r = m_codecvt_facet.in(  
        m_mbs,  
        m_input.m_buffer.begin(),  
        m_input.m_next_available,  
        input_new_start,  
        m_output.m_buffer.begin(),  
        m_output.m_buffer.end(),  
        next_available  
    );  
    BOOST_ASSERT(std::codecvt_base::ok == r); (void)r;  
```  
(note the `(void)r` at the end).  
  
You can put it in other places if you have stylistic reasons for example:  
```  
    (void)r; BOOST_ASSERT(std::codecvt_base::ok == r);  
```  
```  
    (void)r;  
    BOOST_ASSERT(std::codecvt_base::ok == r);  
```  
```  
    BOOST_ASSERT(std::codecvt_base::ok == r);  
    (void)r;  
```  
  
On Sun, Feb 24, 2019 at 8:53 PM Robert Ramey <notifications@github.com>  
wrote:  
  
> I think you can do (void)var; to suppress the warning. It will be clear  
> it is related to the (sometimes compiled) assert.  
>  
> Hmmm - could you expand upon that? You need to be more explicit so I can  
> just paste it in.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/145#issuecomment-466871793>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYD3j6C82aFg1IyynrMwR_DYRxi3nks5vQ2xZgaJpZM4a7IQ4>  
> .  
>

---

## Comment 6

> Username: correaa  
> Created at: 2019-02-25 08:09:54 UTC  
> Updated at: 2019-02-25 08:23:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-466910530  

To make the intention clear you can exploit https://en.cppreference.com/w/cpp/language/attributes/maybe_unused  
  
```  
#ifndef __has_cpp_attribute  
#define __has_cpp_attribute(name) 0  
#endif  
...  
#if __has_cpp_attribute(maybe_unused)  
#define MAYBE_UNUSED [[maybe_unused]]  
#define VOID_UNUSED(var)  
#elif __has_cpp_attribute(gnu::unused)  
#define MAYBE_UNUSED [[gnu::unused]]  
#define VOID_UNUSED(var)  
#else  
#define MAYBE_UNUSED  
#define VOID_UNUSED(var) (void)var;  
#endif  
...  
  MAYBE_UNUSED   
  std::codecvt_base::result r = m_codecvt_facet.in(  
    m_mbs, m_input.m_buffer.begin(),   
    m_input.m_next_available,   
    input_new_start,   
    m_output.m_buffer.begin(),   
    m_output.m_buffer.end(),   
    next_available   
  );  
  BOOST_ASSERT(std::codecvt_base::ok == r);  
  VOID_UNUSED(r);   
```  
  
(...or whatever is the way to detect C++17)

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-02-25 14:31:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-467032362  

There is no need to write such code by hand. Boost has facilities to do this: there is BOOST_ATTRIBUTE_UNUSED in boost/config and there is also `boost/core/ignore_unused.hpp`.

---

## Comment 8

> Username: alfC  
> Created at: 2019-02-25 16:12:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-467072321  

Thanks Hans, after writting the email I wondered if boost would already  
have this. Do you know if BOOST_ATTRIBUTE_UNUSED implies `(void)var`? i.e.  
will really mute the warning? Can you post complete code for RobertR?  
  
  
On Mon, Feb 25, 2019 at 6:31 AM Hans Dembinski <notifications@github.com>  
wrote:  
  
> There is no need to write such code by hand. Boost has facilities to do  
> this: there is BOOST_ATTRIBUTE_UNUSED in boost/config and there is also  
> boost/core/ignore_unused.hpp.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/145#issuecomment-467032362>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYLYFyEf96sf4VM-xM1Fd-5F93qSoks5vQ_PNgaJpZM4a7IQ4>  
> .  
>

---

## Comment 9

> Username: HDembinski  
> Created at: 2019-02-25 16:56:47 UTC  
> Updated at: 2019-02-25 16:57:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-467089323  

I think Robert should use `boost/core/ignore_unused.hpp`, whose usage is demonstrated in the test file in the boost distribution  
`libs/core/test/ignore_unused_test.cpp`  
because this works on every compiler.   
  
According to the docs  
https://www.boost.org/doc/libs/1_69_0/libs/config/doc/html/boost_config/boost_macro_reference.html  
BOOST_ATTRIBUTE_UNUSED only works when the compiler actually supports the corresponding attribute.

---

## Comment 10

> Username: correaa  
> Created at: 2019-02-25 17:04:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-467092509  

Ok, so, if I understand correctly, in Boost, a way to do this is  
  
```  
#include <boost/core/ignore_unused.hpp>  
...  
  BOOST_ATTRIBUTE_UNUSED // redundant with ignore_unused below but clarifies intention  
  std::codecvt_base::result r = m_codecvt_facet.in(  
    m_mbs, m_input.m_buffer.begin(),   
    m_input.m_next_available,   
    input_new_start,   
    m_output.m_buffer.begin(),   
    m_output.m_buffer.end(),   
    next_available   
  );  
  BOOST_ASSERT(std::codecvt_base::ok == r);  
  boost::ignore_unused(r); // see r declaration  
```

---

## Comment 11

> Username: HDembinski  
> Created at: 2019-02-25 18:00:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-467113132  

@correaa Thanks, that looks right to me.

---

## Comment 12

> Username: robertramey  
> Created at: 2019-03-01 23:02:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-468840979  

> To make the intention clear you can exploit https://en.cppreference.com/w/cpp/language/attributes/maybe_unused  
  
No I can't.  the library supports C++ going back to at least C++O3.  Doing this would break that.

---

## Comment 13

> Username: robertramey  
> Created at: 2019-03-01 23:06:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-468841989  

OK - inserting BOOST_ATTRIBUTE_UNUSED - if it passes I'll check it in.

---

## Comment 14

> Username: alfC  
> Created at: 2019-03-01 23:10:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-468842686  

On Fri, Mar 1, 2019 at 3:02 PM Robert Ramey <notifications@github.com>  
wrote:  
  
> To make the intention clear you can exploit  
> https://en.cppreference.com/w/cpp/language/attributes/maybe_unused  
>  
> No I can't. the library supports C++ going back to at least C++O3. Doing  
> this would break that.  
>  
What I mean is that you can rely in new C++ features to *make the intention  
clear*.  
Even if these are features that you can use in the compilation.

---

## Comment 15

> Username: HDembinski  
> Created at: 2019-03-20 07:04:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-474711625  

I am closing this, as the patch is in the develop branch.

---

## Comment 16

> Username: mojca  
> Created at: 2022-11-15 18:05:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/145#issuecomment-1315684864  

It seems that this doesn't have much effect on Visual Studio. I opened a new ticket that is referenced above and I'll try to investigate what exactly is going on.
