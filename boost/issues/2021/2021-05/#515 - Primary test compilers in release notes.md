# #515 - Primary test compilers in release notes [Open]

> Username: jwakely  
> Created at: 2021-05-11 11:05:35 UTC  
> Updated at: 2021-05-13 07:44:28 UTC  
> Url: https://github.com/boostorg/boost/issues/515  

https://www.boost.org/users/history/version_1_76_0.html#version_1_76_0.compilers_tested says:  
  
- GCC: 4.4.7, 4.5.3, 4.6.3, 5.4.0, 8.0.1  
- GCC, C++0x: 4.4.7  
- GCC, C++11: 4.7.3, 4.8.5, 4.9.4, 5.4.0, 6.4.0, 7.1.0, 8.0.1  
- GCC, C++14: 5.4.0, 5.5.0, 6.4.0, 7.1.0, 7.3.0, 8.0.1  
- GCC, C++17: 7.3.0, 8.0.1  
  
If this is correct it's very worrying. None of those releases are still supported or maintained. And 8.0.1 isn't even a release, it's a pre-release snapshot.  
  
Please either update it to be accurate, or consider testing on more modern systems, e.g. RHEL 8 uses GCC 8.4, Ubuntu 20.04 uses GCC 9.2

---

## Comment 1

> Username: jwakely  
> Created at: 2021-05-11 11:08:20 UTC  
> Url: https://github.com/boostorg/boost/issues/515#issuecomment-838283411  

I should have mentioned that it also lists "additional test compilers" ... but it's still nothing current:  
  
  
- GCC: 4.4.7, 4.5.3, 4.6.3, 4.9.4, 5.4.0, 5.5.0, 8.0.1  
- GCC, C++0x: 4.4.7  
- GCC, C++11: 4.7.3, 4.8.5, 4.9.4, 5.4.0, 6.4.0, 7.1.0, 8.0.1  
- GCC, C++14: 5.4.0, 5.5.0, 6.3.0, 6.4.0, 7.1.0, 7.3.0, 8.0.1, 8.1.0  
- GCC, C++17: 7.3.0, 8.0.1

---

## Comment 2

> Username: mclow  
> Created at: 2021-05-13 02:42:36 UTC  
> Url: https://github.com/boostorg/boost/issues/515#issuecomment-840247661  

> Please either update it to be accurate, or consider testing on more modern systems, e.g. RHEL 8 uses GCC 8.4, Ubuntu 20.04 uses GCC 9.2  
  
I will look into this.  I suspect that the list is sadly out of date.

---

## Comment 3

> Username: jwakely  
> Created at: 2021-05-13 07:44:28 UTC  
> Url: https://github.com/boostorg/boost/issues/515#issuecomment-840384303  

Yes I think so, but stating such precise versions gives a misleading impression that it's accurate (and so not well tested).  
  
Maybe a solution would be to just add something like "at least these versions" and link to the test results pages?
