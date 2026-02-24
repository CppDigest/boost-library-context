# #279 - Logic bug in detail::win32::create_event() [Closed]

> Username: chrullrich  
> Created at: 2019-04-12 12:29:06 UTC  
> Updated at: 2019-04-13 07:14:21 UTC  
> Closed at: 2019-04-13 07:14:21 UTC  
> Url: https://github.com/boostorg/thread/issues/279  

https://github.com/boostorg/thread/blob/a645ef761ddf4c9811eb707c479363a79ae4534e/include/boost/thread/win32/thread_primitives.hpp#L105  
  
The bitwise-or operator has higher precedence than the ternary, so this line compiles as  
  
    type ? create_event_manual_reset : ( ( 0 | state ) ? create_event_initial_set : 0 )  
  
Creating an initially-set manual-reset event is therefore not possible. What is probably meant is:  
  
    ( type ? create_event_manual_reset : 0 ) | ( state ? create_event_initial_set : 0 )

---

## Comment 1

> Username: viboes  
> Created at: 2019-04-12 17:12:12 UTC  
> Url: https://github.com/boostorg/thread/issues/279#issuecomment-482652087  

Right, thanks for the report.  
  
  
PR is welcome.
