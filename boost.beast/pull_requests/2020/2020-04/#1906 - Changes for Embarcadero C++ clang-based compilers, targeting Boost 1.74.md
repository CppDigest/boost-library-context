# #1906 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Closed]

> Username: eldiener  
> Created at: 2020-04-20 04:24:01 UTC  
> Updated at: 2020-08-20 13:27:55 UTC  
> Closed at: 2020-05-07 01:46:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1906  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 12:10:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624611846  

The failures in Travis CI tests, such as:  
  
"/boost/asio/ssl/impl/host_name_verification.ipp:57:52: error: ‘X509_check_ip_asc’ was not declared in this scope  
  
     return X509_check_ip_asc(cert, host_.c_str(), 0) == 1;  
  
                                                    ^  
  
./boost/asio/ssl/impl/host_name_verification.ipp:63:50: error: ‘X509_check_host’ was not declared in this scope  
  
         host_.c_str(), host_.size(), 0, &peername);"  
  
have nothing to do with this PR.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-05-06 13:35:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624653176  

The directory test/extern/zlib-1.2.11 is an exact copy of that version of ZLib. Your changes would effectively be forking zlib. The place to make these changes is in the upstream zlib repository, by submitting a pull request there, not here, as we don't want to maintain a fork of zlib. I have squashed this pull request down and removed the changes to zlib, this is what is left:  
https://github.com/vinniefalco/beast/commits/embarcadero

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-05-06 15:27:08 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624717558  

I agree in principal but disagree in practicality. Are you tracking zlib so every time they make any change, you incorporate your change into your code ? Why must Boost wait for a 3rd party library to correct their code in order to correct its own code. If we wait we will be preventing Boost from working in a situation where it should work, alll because you feel obligated to depend on a 3rd part library's bug(s). And if the 3rd party library never corrects its code, for whatever reason they may have, why should a Boost library suffer from it so that some compiler configuration does not work with that library.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-05-06 15:55:40 UTC  
> Updated_at: 2020-05-06 15:56:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624734086  

> Why must Boost wait for a 3rd party library to correct their code in order to correct its own code  
  
This copy of zlib is not used in the library, it is used for the tests - to compare our implementation against the zlib implementation and ensure there are no regressions. I do have plans to update it to the newest zlib, I simply haven't done so yet.

---

## Comment 5

> Username: eldiener  
> Created_at: 2020-05-06 17:33:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624786775  

>   
>   
> > Why must Boost wait for a 3rd party library to correct their code in order to correct its own code  
>   
> This copy of zlib is not used in the library, it is used for the tests - to compare our implementation against the zlib implementation and ensure there are no regressions. I do have plans to update it to the newest zlib, I simply haven't done so yet.  
  
I am working on a PR for zlib. The latest zlib in 'develop' is about 3 years of fixes over what we have, but basically the same fixes for Embarcadero C++ clang-based compilers will apply.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2020-05-06 17:42:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624791704  

That's great, but do you agree that the severity of this problem with respect to the copy of zlib in Beast is not as severe as originally thought? In other words, the only users affected are the ones trying to build and run the Beast zlib tests under Embarcadero clang. Beast users who are simply consuming the library under Embarcadero clang are unaffected.

---

## Comment 7

> Username: eldiener  
> Created_at: 2020-05-06 17:46:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624793269  

>   
>   
> That's great, but do you agree that the severity of this problem with respect to the copy of zlib in Beast is not as severe as originally thought? In other words, the only users affected are the ones trying to build and run the Beast zlib tests under Embarcadero clang. Beast users who are simply consuming the library under Embarcadero clang are unaffected.  
  
Yes, of course. But I am an Embarcadero C++ clang-based compiler b2 tester <g> so it affects me. OK, this can wait or be closed if you think it is not important enough.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2020-05-06 17:54:45 UTC  
> Updated_at: 2020-05-06 17:55:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624797616  

> I am an Embarcadero C++ clang-based compiler b2 tester so it affects me  
  
Do you mean "tester" as in, a machine that runs in the Boost test matrix here?  
https://www.boost.org/development/tests/master/developer/beast.html  
  
If so then yes we need to address it. Rather than change zlib, I would prefer to change the test itself. We would need to add appropriate `#ifdef` to prevent the affected .cpp files from being compiled under Embarcadero clang. And we should escalate the priority on updating to the newer zlib (this requires examining each commit in the newer zlib and porting it to Beast).  
  
Also note that we will still pick up the library change (`std::FILE`) since that is in the public headers.

---

## Comment 9

> Username: madmongo1  
> Created_at: 2020-05-06 17:59:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624800285  

👍

---

## Comment 10

> Username: eldiener  
> Created_at: 2020-05-07 01:46:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1906#issuecomment-624981298  

>   
>   
> > I am an Embarcadero C++ clang-based compiler b2 tester so it affects me  
>   
> Do you mean "tester" as in, a machine that runs in the Boost test matrix here?  
> https://www.boost.org/development/tests/master/developer/beast.html  
>   
> If so then yes we need to address it. Rather than change zlib, I would prefer to change the test itself. We would need to add appropriate `#ifdef` to prevent the affected .cpp files from being compiled under Embarcadero clang. And we should escalate the priority on updating to the newer zlib (this requires examining each commit in the newer zlib and porting it to Beast).  
>   
> Also note that we will still pick up the library change (`std::FILE`) since that is in the public headers.  
  
I am going to close this PR and I will submit another one which just has the std::FILE change in it. In the meantime if I can get zlib working for Embarcadero I will submit my PR to zlib and if they merge it sometime in the future I hope you will pick it up.

---
