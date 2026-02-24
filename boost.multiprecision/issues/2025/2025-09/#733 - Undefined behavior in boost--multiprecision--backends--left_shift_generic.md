# #733 - Undefined behavior in boost::multiprecision::backends::left_shift_generic [Closed]

> Username: nvswarren  
> Created at: 2025-09-04 22:27:34 UTC  
> Updated at: 2025-09-08 17:11:53 UTC  
> Closed at: 2025-09-08 10:50:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733  

I am not sure whether this is (a) a bug in Boost that more recent gcc detect, or (b) a bug in g++'s optimization that falsely detects a bug in Boost. Either way, it looks like gcc believes that the loop in left_shift_generic can run a large number of times, which would eventually create an out-of-bounds access (??), which triggers a compilation error. Hacking the Boost source to make it clearer to g++ that the loop can't run that many times (or perhaps: fixing a Boost bug that allows the loop to run too many times?) solves/hides the issue.  
  
`repro.cpp`:  
```  
/*  
Failure mode:  
    ...  
        inlined from 'BigInt calcm(size_t, size_t)' at repro.cpp:46:24,  
        inlined from 'void f(S*, S*)' at repro.cpp:51:36:  
    /usr/include/boost/multiprecision/cpp_int/bitwise.hpp:441:26: error: iteration 2305843009213693951 invokes undefined behavior [-Werror=aggressive-loop-optimizations]  
      441 |       pr[rs - 1 - i] = pr[rs - 1 - i - offset] << shift;  
          |                        ~~^  
    /usr/include/boost/multiprecision/cpp_int/bitwise.hpp:439:18: note: within this loop  
      439 |    for (; rs - i >= 2 + offset; ++i)  
      |           ~~~~~~~^~~~~~~~~~~~~  
  
// ubuntu:22.04     compiles  
// ubuntu:24.04     fails with the UB error  
// ubuntu:25.04     fails with the UB error plus a memcpy error  
// debian:11        compiles  
// debian:12        fails with                   a memcpy error  
// debian:13        fails with the UB error plus a memcpy error  
// debian:testing   fails with the UB error plus a memcpy error (2025/09/04)  
// debian:unstable  fails with the UB error plus a memcpy error (2025/09/04)  
docker run -it --rm -v $(pwd):/data ubuntu:24.04  
  
apt update  
apt -y install libboost-dev g++  
cd /data  
g++ -O0 -fdiagnostics-color=always -Wall -Wextra -Wno-overloaded-virtual \  
    -Wno-deprecated -Wno-deprecated-declarations -pedantic -O3 -DNDEBUG \  
    -Wall -Wextra -Wno-overloaded-virtual -Wno-deprecated \  
    -Wno-deprecated-declarations -pedantic -Werror -Wall -Wextra -Wswitch-enum \  
    -std=gnu++20 \  
    -c -o repro repro.cpp  
  
25.04: Adding -fno-aggressive-loop-optimizations solves/hides the warning,  
but not the 25.04 memcpy error.  
  
24.04: The following edit to the Boost source solves/hides the warning:  
    sed -i -e 's/rs - i >= 2/rs > i \&\& rs - i >= 2/' \  
        /usr/include/boost/multiprecision/cpp_int/bitwise.hpp  
*/  
  
#include <boost/multiprecision/cpp_int.hpp>  
#include <iostream>  
  
using BigInt = boost::multiprecision::cpp_int;  
  
struct S {  
    unsigned sb;  
    unsigned n;  
    BigInt value;  
};  
  
static unsigned eb(S* i) {  
    return i->sb + i->n - 1;  
}  
  
// If this is a plain function declaration with the implementation in another C++ file,  
// g++ can't inline this. Making this change also solves/hides the compilation error.  
static inline BigInt calcm(size_t sb, size_t eb) {  
    return ((BigInt(1) << (eb - sb + 1)) - 1) << sb;  
}  
  
void f(S* i1, S* i2) {  
    unsigned newsb = std::min(i1->sb, i2->sb);  
    BigInt m = calcm(i2->sb, eb(i2));  
  
    i1->value <<= i1->sb;  
    i1->value >>= newsb;  
  
    // Replacing the previous 2 lines with the following 2 lines solves/hides the issue.  
    //i1->value = i1->value << i1->sb;  
    //i1->value = i1->value >> newsb;  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-09-05 07:27:31 UTC  
> Updated at: 2025-09-05 07:35:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3257355896  

This seems interesting. Do you have an isolated code sample that gets the warning and/or reaches undefined-behavior?  
  
Which exact compiler are you using? Here I mean which GCC version? You can query that with `g++ -v`.  
  
In your compilation command, I believe we also might detect that you have two optimization flags set, both `-O0` as well as `-O3`. Admittedly, I forgot what GCC does when two optimization flags are set. Which one do you really want?

---

## Comment 2

> Username: nvswarren  
> Created at: 2025-09-05 16:02:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3258886882  

That last -O overrides any earlier ones.  
  
We initially reproduced on Ubuntu 24.04:  
* g++ (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0  
* libboost1.83-dev:amd64        1.83.0-2.1ubuntu3.1  
  
This code sample is pretty minimal. Maybe we can get rid of the calls to min and calcm; I will try and see.

---

## Comment 3

> Username: nvswarren  
> Created at: 2025-09-05 16:16:02 UTC  
> Updated at: 2025-09-05 16:20:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3258927349  

Oh, it turns out the/a repro comes from `calcm()` not `f()` (`calcm` was just inlined into `f` so I thought the issue came from there). So the following is a more minimal repro:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
  
using BigInt = boost::multiprecision::cpp_int;  
  
BigInt calcm(unsigned ls) {  
    return BigInt(1) << ls;  
}  
```  
  
If the shift is a literal/constant, the issue doesn't arise; it has to be a runtime variable.

---

## Comment 4

> Username: nvswarren  
> Created at: 2025-09-05 16:17:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3258931816  

Oh, and I validated that with just `-O0` the issue is not reported by gcc, but with just `-O3` it is.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2025-09-06 11:44:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3261932673  

OK. Thanks for adding such great clarifying notes. I'm busy with anjother issue at the moment, and will try to look into this soon.  
  
Often times, we encounter similar compiler warnings. Sometimes they turn out to be really good catches. Some other times, however, the warning may be bogus. Then we need to (or try to) sensible justify disabling the warning with code annotations.  
  
We will get to studying this soon. Thank you for reporting @nvswarren   
  
Cc: @jzmaddock

---

## Comment 6

> Username: ckormanyos  
> Created at: 2025-09-08 08:29:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3265167950  

OK with the test code below (based on the reduced sample), I can reproduce the warning.  
  
```  
In file included from /mnt/c/ChrisGitRepos/modular_boost/multiprecision/include/boost/multiprecision/cpp_int.hpp:2354,  
                 from ./test.cpp:1:  
/mnt/c/ChrisGitRepos/modular_boost/multiprecision/include/boost/multiprecision/cpp_int/bitwise.hpp: In function ‘BigInt calcm(unsigned int)’:  
/mnt/c/ChrisGitRepos/modular_boost/multiprecision/include/boost/multiprecision/cpp_int/bitwise.hpp:443:26: warning: iteration 2305843009213693951 invokes undefined behavior [-Waggressive-loop-optimizations]  
  443 |       pr[rs - 1 - i] = pr[rs - 1 - i - offset] << shift;  
      |                        ~~^  
/mnt/c/ChrisGitRepos/modular_boost/multiprecision/include/boost/multiprecision/cpp_int/bitwise.hpp:439:102: note: within this loop  
  439 |    for (; static_cast<std::size_t>(static_cast<std::ptrdiff_t>(rs) - static_cast<std::ptrdiff_t>(i)) >= static_cast<std::size_t>(2 + static_cast<std::ptrdiff_t>(offset)); ++i)  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
  
extern unsigned left_shift_amount;  
  
using BigInt = boost::multiprecision::cpp_int;  
  
auto calcm(unsigned ls) -> BigInt  
{  
  return BigInt(1) << ls;  
}  
  
auto main() -> int  
{  
  calcm(left_shift_amount);  
}  
  
unsigned left_shift_amount { UINT8_C(3) };  
```

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-09-08 08:34:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3265188039  

It can be repaired with code annotation or with casting the loop-index and its comparisons to signed `ptrdiff_t`. I think the latter might be a better choice. I will run the proposed repair through CI later today.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2025-09-08 09:32:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3265420453  

See #737

---

## Comment 9

> Username: nvswarren  
> Created at: 2025-09-08 16:38:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3267104179  

Thanks for the quick fix. I have verified that it solves the compilation issue in our environment.  
  
Is there a mechanism to cherry-pick the fix back to a 1.83.x release branch so that it could be incorporated into a fixed 1.83 package within Ubuntu 24.04? Or would the Ubuntu packager need to manually add this patch to their package i.e. apply it locally during the package build?

---

## Comment 10

> Username: ckormanyos  
> Created at: 2025-09-08 16:56:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3267161850  

> Thanks for the quick fix. I have verified that it solves the compilation issue in our environment.  
  
OK good. That's positive. Thank you for reporting the (unfortunate) issue.  
  
> Is there a mechanism to cherry-pick the fix back to a 1.83.x release branch so that it could be incorporated into a fixed 1.83 package within Ubuntu 24.04? Or would the Ubuntu packager need to manually add this patch to their package i.e. apply it locally during the package build?  
  
That's a good one. We really _would_ want this compiler-warning to be abstent from the LTS. But to be honest, I'm not sure if we could ever re-release a patch. There is not much support for that in our community.  
  
I was thinking of your use case when correcting this and tried to limit the scope of change to as few lines as possiblr. We got 1 line changed. i don't know how to manage that in 1.83 for the LTS, since it's a new line of code.  
  
Maybe the guys have a good idea how to proceed?  
  
Cc: @jzmaddock and @mborland

---

## Comment 11

> Username: mborland  
> Created at: 2025-09-08 17:01:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3267174930  

I've only ever seen the Debian or Ubuntu maintainers back port commits if something is completely broken. Boost itself doesn't do point releases.

---

## Comment 12

> Username: nvswarren  
> Created at: 2025-09-08 17:11:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/733#issuecomment-3267205247  

OK, I requested a rebuild on Launchpad; we'll see if it's possible.  
https://bugs.launchpad.net/ubuntu/+source/boost-defaults/+bug/2122352
