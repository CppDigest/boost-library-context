# #28 - std::bit_not and other C++17 entities used in pre-C++17 environment [Closed]

> Username: joaquintides  
> Created at: 2022-01-27 12:00:34 UTC  
> Updated at: 2022-02-13 12:09:09 UTC  
> Closed at: 2022-02-12 20:37:21 UTC  
> Url: https://github.com/boostorg/bind/issues/28  

As deduced from [here](https://github.com/boostorg/multi_index/runs/4963411105?check_suite_focus=true), `BOOST_CXX_VERSION` >= 201700L for Clang 5.0 with `-std=c++1z`, yet `std::bit_not`  etc. are not available in that environment, which causes compilation errors with `include/boost/bind/detail/result_traits.hpp`.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-01-27 14:32:49 UTC  
> Url: https://github.com/boostorg/bind/issues/28#issuecomment-1023271905  

"Although std::bit_not is added via post-C++11 proposal N3421, it is treated as a part of the resolution for LWG issue 660 (except for its transparent specialization std::bit_not<>) by common implementations, and thus available in their C++98/03 mode."  
  
Probably need to guard the libstdc++ version. You seem to be using 4.8 in that config, which is an odd choice for a C++17 target (as 4.8 is barely C++11).

---

## Comment 2

> Username: pdimov  
> Created at: 2022-01-27 15:27:14 UTC  
> Url: https://github.com/boostorg/bind/issues/28#issuecomment-1023333022  

> /usr/bin/docker pull ubuntu:14.04  
  
And using Ubuntu 14.04, at that. :-) FWIW, clang 5.0 from 18.04 works; it probably installs a newer libstdc++ automatically.

---

## Comment 3

> Username: joaquintides  
> Created at: 2022-02-12 09:40:26 UTC  
> Url: https://github.com/boostorg/bind/issues/28#issuecomment-1037091276  

Hi, are you planning on tackling this one? Or should I just migrate my offending test case from Ubuntu 14.04 to 18.04 and forget about it?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-02-12 15:44:43 UTC  
> Url: https://github.com/boostorg/bind/issues/28#issuecomment-1037262915  

I will fix it. Sorry about forgetting about it, too many other things to do, and thanks for the reminder.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-02-12 20:37:54 UTC  
> Url: https://github.com/boostorg/bind/issues/28#issuecomment-1037461526  

Should be fixed on develop. If it isn't, please let me know.

---

## Comment 6

> Username: joaquintides  
> Created at: 2022-02-13 12:09:09 UTC  
> Url: https://github.com/boostorg/bind/issues/28#issuecomment-1038061980  

I confirm the problem's solved. Thank you!
