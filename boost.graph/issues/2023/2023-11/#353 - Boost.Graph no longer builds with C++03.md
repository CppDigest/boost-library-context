# #353 - Boost.Graph no longer builds with C++03 [Closed]

> Username: mclow  
> Created at: 2023-11-10 17:30:06 UTC  
> Updated at: 2025-07-24 22:50:04 UTC  
> Closed at: 2025-07-24 22:50:04 UTC  
> Url: https://github.com/boostorg/graph/issues/353  

It includes `lexical_cast.h` which no longer supports C++03.

---

## Comment 1

> Username: mclow  
> Created at: 2023-11-10 17:30:35 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-1806141864  

Discovered in Boost 1.84.0 Beta 1 Release Candidate 1

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2023-11-12 05:58:32 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-1807013809  

Is that actually an issue though? I thought all libraries were free to dispense with supporting pre-C++11 now? I've already taken the C+98 and C++03 CI builders out of the configurations for Drone and GitHub actions.

---

## Comment 3

> Username: mclow  
> Created at: 2023-11-12 15:00:01 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-1807151808  

If you decide that Boost.Graph is no longer supporting c++03, that's your call.     
  
But when I look at the docs and the meta/library.json file, I don't see any suggestion of that.  
  
Many libraries have (or are in the process of) dispensing with C++03 support.  
I see messages like this in my build logs:  
  
./boost/smart_ptr/detail/requires_cxx11.hpp:19:1: warning: C++03 support was deprecated in Boost.SmartPtr 1.82 and will be removed in Boost.SmartPtr 1.84. Please open an issue in https://github.com/boostorg/smart_ptr if you want it retained. [-W#pragma-messages]

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2023-11-12 22:00:13 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-1807252987  

OK, good point, I should communicate it more explicitly.

---

## Comment 5

> Username: andrea-cassioli-maersk  
> Created at: 2024-04-17 09:24:34 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-2060811043  

What has been the decision here? I see still 03 in the meta/library.json file.

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2024-04-17 10:12:03 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-2060908995  

The decision is to drop C++03, the fact is I haven't had time to figure out how to do it.

---

## Comment 7

> Username: andrea-cassioli-maersk  
> Created at: 2025-07-23 10:37:02 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-3106927838  

I guess this issue is settled now. Shall it be close?

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2025-07-23 23:07:08 UTC  
> Url: https://github.com/boostorg/graph/issues/353#issuecomment-3111021030  

Yes, I'm pretty sure I actually fixed this by updating the metadata.
