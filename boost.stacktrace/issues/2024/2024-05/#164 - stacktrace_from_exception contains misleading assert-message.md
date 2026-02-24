# #164 - stacktrace_from_exception contains misleading assert-message [Closed]

> Username: Ukilele  
> Created at: 2024-05-23 19:51:20 UTC  
> Updated at: 2024-06-10 19:24:44 UTC  
> Closed at: 2024-06-10 19:24:21 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/164  

There is a BOOST_ASSERT_MSG here: https://github.com/boostorg/stacktrace/blob/39afcefb6413b773b9f464689e994698a7a2ddc8/src/from_exception.cpp#L236-L238  
  
Isn't `sizeof(void*)` equal to 4 on 32bit platforms and 8 on 64bit platforms? The assert-message reads contrary to its condition, doesn't it? I ran into this assert on a 64bit platform and got confused, because it says `"32bit platforms are unsupported [...]"`.

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-06-10 19:24:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/164#issuecomment-2159128551  

Ouch.  
  
Many thanks for the bugreport, fixed in upstream
