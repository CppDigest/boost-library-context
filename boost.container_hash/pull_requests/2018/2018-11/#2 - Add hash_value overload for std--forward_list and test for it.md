# #2 Add hash_value overload for std::forward_list and test for it [Closed]

> Username: zamazan4ik  
> Created at: 2018-11-12 19:35:42 UTC  
> Updated at: 2022-09-19 10:23:02 UTC  
> Closed at: 2022-09-19 10:23:02 UTC  
> Url: https://github.com/boostorg/container_hash/pull/2  

Hello.  
In this PR I just add missing from my point of view hash_value overload for std::forward_list.  
  
Please check it and I did something wrong - correct me :)  
  
Thank you.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-11-13 00:07:32 UTC  
> Url: https://github.com/boostorg/container_hash/pull/2#issuecomment-438078182  

Alex - the tests have failed; seems they want to `push_back` on a `forward_list`.

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2018-11-13 09:34:59 UTC  
> Url: https://github.com/boostorg/container_hash/pull/2#issuecomment-438198915  

Yeah, I see... Ok, I will fix it soon.

---

## Comment 3

> Username: zamazan4ik  
> Created_at: 2018-12-05 21:44:27 UTC  
> Url: https://github.com/boostorg/container_hash/pull/2#issuecomment-444661460  

@mclow I fixed the issue with `push_front`. but test on Travis CI still fails (on Windows works fine). And log on Travis is strange - seems like there is no `hash_value` overload for `std::forward_list`. Probably `BOOST_NO_CXX11_HDR_FORWARD_LIST` macros is not defined on Linux or anything else. Can you look into the issue please?  
  
Thank you.

---

## Comment 4

> Username: mclow  
> Created_at: 2018-12-07 21:43:16 UTC  
> Url: https://github.com/boostorg/container_hash/pull/2#issuecomment-445374789  

It seems to me that your test needs to be conditioned on `BOOST_NO_CXX11_HDR_FORWARD_LIST`.

---
