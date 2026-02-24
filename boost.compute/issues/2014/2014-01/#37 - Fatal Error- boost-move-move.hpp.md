# #37 - Fatal Error: boost/move/move.hpp [Closed]

> Username: aditya4d  
> Created at: 2014-01-05 23:08:42 UTC  
> Updated at: 2014-01-21 02:49:46 UTC  
> Closed at: 2014-01-06 02:37:12 UTC  
> Url: https://github.com/boostorg/compute/issues/37  

Hi, when I compile `test_device.hpp`, I am getting this error. The location of move.hpp in my system is at `/usr/include/boost/variant/detail/move.hpp`

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-01-06 00:02:09 UTC  
> Url: https://github.com/boostorg/compute/issues/37#issuecomment-31619990  

Hi Aditya,  
  
Thanks for the report.  
  
It looks like Boost.Move was added in Boost version 1.48. Would you be able to update your system to use that version? Otherwise I can look into optionally using Boost.Move only when available.  
  
Thanks,  
Kyle

---

## Comment 2

> Username: aditya4d  
> Created at: 2014-01-06 02:37:12 UTC  
> Url: https://github.com/boostorg/compute/issues/37#issuecomment-31623805  

Hi Kyle,  
  
I used `sudo apt-get install libboost1.48-all-dev` it worked.

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-01-06 02:38:08 UTC  
> Url: https://github.com/boostorg/compute/issues/37#issuecomment-31623828  

Cool. I'll update the CMakeList to check for Boost 1.48 or later.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-01-21 02:49:46 UTC  
> Url: https://github.com/boostorg/compute/issues/37#issuecomment-32816994  

For reference, I've added a Boost miniumum version check to the Boost.Compute headers. See 47922aa780ae2aa493ad0b22a968c1e05e602777.
