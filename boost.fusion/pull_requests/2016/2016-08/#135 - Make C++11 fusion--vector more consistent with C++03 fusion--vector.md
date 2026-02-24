# #135 Make C++11 fusion::vector more consistent with C++03 fusion::vector [Merged]

> Username: vtnerd  
> Created at: 2016-08-26 15:36:15 UTC  
> Updated at: 2017-07-26 08:30:27 UTC  
> Merged at: 2016-10-14 04:51:52 UTC  
> Closed at: 2016-10-14 04:51:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/135  

This patch fixes the issues on both the master and develop branch (the develop branch is broken in a different way after my last attempt to fix the bug present on master). To reduce the likelihood of creating future issues, I looked at C++03 vector carefully and tried to mimic its behavior closely. This patch has an enormous change for tests, and both the develop and master branch will fail on these tests. I hope the tests are not too complex themselves.  
  
I have tested this patch on Clang3.5-OSX, Clang3.7-Linux, and Gcc4.9-Linux. Variadic vector is currently disabled for all MSVC versions.  
### Performance Timings  
  
Intel Core 2 Duo T5250 (1.5 Ghz, 2MB Cache), 4GiB RAM, 64-bit Gentoo Linux. For each case, 5 trials were run and the last 3 were averaged for the tables below.  
#### Construction  
  
``` c++  
int main() {  
    boost::fusion::vector<[1000 entries]> foo([1000 entries]);  
}  
```  
  
|  | Gcc 4.9 | Clang 3.7 |  
| --- | --- | --- |  
| Master | 24.052s | 5.981s |  
| Develop | 25.393s | 5.940s |  
| Patch | 15.705s | 6.712s |  
#### Copy using Sequence Constructor  
  
``` c++  
int main() {  
    boost::fusion::vector<[999 entries], int> foo(  
        boost::fusion::vector<[999 entries], long>([1000 entries])  
    );  
}  
```  
  
|  | Gcc 4.9 | Clang 3.7 |  
| --- | --- | --- |  
| Master | 5m20.441s | 49.498s |  
| Develop | 5m6.216s | 49.812s |  
| Patch | 5m40.036s | 50.446s |  
  
> If the vector types are identical, then the default copy-constructor is used, and the timings are very similar to the construction case.

---

## Comment 1

> Username: vtnerd  
> Created_at: 2016-08-26 15:36:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/135#issuecomment-242770005  

Perhaps @flast and @K-ballo would like to review this large patch. Most changes are to tests!

---

## Comment 2

> Username: Flast  
> Created_at: 2016-10-14 01:38:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/135#issuecomment-253687502  

It seems good, I'll merge after test.

---

## Comment 3

> Username: Flast  
> Created_at: 2016-10-14 04:51:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/135#issuecomment-253709107  

Confirmed,  
- GCC 6.2.1 20160916 (Fedora rawhide)  
  - gnu++98, gnu++11, gnu++14, gnu++1z  
- Clang 3.8.0 (Fedora rawhide)  
  - gnu++98, gnu++11, gnu++14, gnu++1z  
- MSVC 8, 9, 10, 11, 12, 14  
  
Thank you @vtnerd !

---

## Comment 4

> Username: vtnerd  
> Created_at: 2017-07-25 19:31:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/135#issuecomment-317847219  

@DanielaE good catch - how did you spot this? Anyway, see [PR](https://github.com/boostorg/fusion/pull/148).

---

## Comment 5

> Username: DanielaE  
> Created_at: 2017-07-26 08:30:27 UTC  
> Url: https://github.com/boostorg/fusion/pull/135#issuecomment-317986135  

I ran the testsuite with msvc 14.1 at warning level 4 (roughly similar to clang/gcc -wall -wextra + some more) which exposes such errors. If everything else is warning-free at this level (that requires a lot of changes to other boost libraries!) any compiler warning sticks out like a lighthouse at night.  
  
Thanks for fixing the issue!

---
