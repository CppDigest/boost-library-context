# #120 Add BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_DISABLE_LIBC_WRAPPER macro [Merged]

> Username: volo-zyko  
> Created at: 2021-07-01 13:43:45 UTC  
> Updated at: 2022-06-07 12:10:57 UTC  
> Merged at: 2022-06-07 12:10:57 UTC  
> Closed at: 2022-06-07 12:10:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/120  

Currently there is no way to use `return ::syscall(SYS_getrandom, buf, size, flags);` implementation for this functionality at the end of file without patching it. New macro allows to switch to that branch.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-07-01 14:16:21 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872285752  

What is the rationale for force-enabling the syscall implementation? If there is a compatibility issue with some libc or something like that, it'd be better to improve the detection logic instead.

---

## Comment 2

> Username: volo-zyko  
> Created_at: 2021-07-01 15:04:50 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872322978  

For example, we target glibc 2.17 which didn't have `getrandom` (it's available only in 2.25+) but we build on Ubuntu 18.04 with glibc 2.27 and we want to force the syscall version.  
  
> it'd be better to improve the detection logic  
  
In my case I don't see how to do this.  
  
Another note, what's the point in having dead code in the implementation?

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-07-01 15:40:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872349840  

> Another note, what's the point in having dead code in the implementation?  
  
What you mean by dead code? On a given platform, only one code branch is enabled.

---

## Comment 4

> Username: volo-zyko  
> Created_at: 2021-07-01 18:54:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872475677  

> What you mean by dead code?  
  
Sorry, it was a premature conclusion. I needed to get to this code branch.  
```cpp  
#else  
        return ::syscall(SYS_getrandom, buf, size, flags);  
#endif  
```  
But in the setup I described earlier it's impossible or I don't see how to do it. I don't have `BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_IMPL_GETRANDOM` defined and my glibc version (in the build environment) is greater than 2.25 hence I get `BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_HAS_LIBC_WRAPPER` defined. Consequently my code compiles with:  
```cpp  
#elif defined(BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_HAS_LIBC_WRAPPER)  
        return ::getrandom(buf, size, flags);  
#else  
```  
The resulting binary cannot run on systems with glibc 2.17.

---

## Comment 5

> Username: Lastique  
> Created_at: 2021-07-01 19:56:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872510895  

I see. Disclaimer: I'm not the maintainer. Although I personally don't think that what you're doing is correct (IMHO, you should build your code on a platform that matches or older than that it will run on), if the maintainer is willing to support your case, the patch looks reasonable to me. Minor nitpick: I would rearrange the preprocessor conditions to avoid an empty branch.

---

## Comment 6

> Username: Lastique  
> Created_at: 2021-07-01 20:10:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872519169  

On the second thought, no. `BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_IMPL_GETRANDOM` was not really intended to be user-configurable, so `BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_IMPL_SYSCALL` doesn't look like a user-definable either.  There are a number of user-definable `BOOST_UUID_RANDOM_PROVIDER_DISABLE_*` macros that are checked in `random_provider_detect_platform.hpp` (e.g. `BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETRANDOM`). We could introduce a new one, like `BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETRANDOM_LIBC` or something.  
  
Also, the new macro should probably be documented. Although, it looks like, none of the `BOOST_UUID_RANDOM_PROVIDER_DISABLE_*` macros are documented currently, so the docs could be added for all of them separately.

---

## Comment 7

> Username: volo-zyko  
> Created_at: 2021-07-01 20:43:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872537636  

> you should build your code on a platform that matches or older than that it will run on  
  
This is one of the options. Actually we did it previously and we've learned its drawbacks the hard way. Building on old systems modern C++ requires building binutils, compiler and whatnot. There are many kinds of incompatibilities. No one normally cares about such things as building modern compiler on ancient systems - glibc 2.17 is from 2013. :-) We've had it enough in the last 6 years or so. Now we want to try a different technique described [here](https://ftp.gnu.org/old-gnu/Manuals/ld-2.9.1/html_chapter/ld_3.html#SEC25). We hope that building only our code with certain asm directives will relief from the burden of maintaining patches in the toolchain.  
  
Regarding the proposed patch, I just thought that it might be useful to the community.  
  
> On the second thought, no.  
  
I'll take a look at `random_provider_detect_platform.hpp`, thanks.

---

## Comment 8

> Username: volo-zyko  
> Created_at: 2021-07-02 09:00:20 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872839209  

Ok, I took a look at `random_provider_detect_platform.hpp`, specifically at this code snippet:  
  
```cpp  
#elif defined(BOOST_UUID_RANDOM_PROVIDER_HAS_GETRANDOM) && !defined(BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX) && !defined(BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETRANDOM)  
# define BOOST_UUID_RANDOM_PROVIDER_GETRANDOM  
# define BOOST_UUID_RANDOM_PROVIDER_NAME getrandom  
  
#elif BOOST_LIB_C_GNU >= BOOST_VERSION_NUMBER(2, 25, 0) && !defined(BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX) && !defined(BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETENTROPY)  
# define BOOST_UUID_RANDOM_PROVIDER_GETENTROPY  
# define BOOST_UUID_RANDOM_PROVIDER_NAME getentropy  
  
#else  
# define BOOST_UUID_RANDOM_PROVIDER_POSIX  
# define BOOST_UUID_RANDOM_PROVIDER_NAME posix  
```  
I do still want to execute `getrandom`-specific code path. If I wanted to switch to POSIX, I had to define `BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX` (or define both `BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETRANDOM` and `BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETENTROPY`). So, I don't see a point in changing anything here. Instead, I still have to change the `random_provider_getrandom.ipp` but maybe with a renamed macro and with a more nicely arranged code.

---

## Comment 9

> Username: Lastique  
> Created_at: 2021-07-02 09:09:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-872845406  

> I still have to change the `random_provider_getrandom.ipp` but maybe with a renamed macro and with a more nicely arranged code.  
  
Yes, that's what I meant.

---

## Review 10 [Changes requested]

> Username: jeking3  
> Created_at: 2022-01-25 13:22:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/uuid/pull/120#pullrequestreview-862269060  

Hello - the documentation for "Preprocessor Macros" should be updated to indicate this additional option.  Thanks.

---

## Comment 11

> Username: jeking3  
> Created_at: 2022-02-09 21:09:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-1034194418  

@volo-zyko also you will want to rebase on develop so tests run, and add a test to cover the new BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_DISABLE_LIBC_WRAPPER compile-time branch.

---

## Comment 12

> Username: volo-zyko  
> Created_at: 2022-02-11 06:55:43 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-1035927371  

It's not clear what's wrong with [`posix (gcc-4.4, 98,0x, ubuntu-20.04, ubuntu:16.04)`](https://github.com/boostorg/uuid/runs/5147950931?check_suite_focus=true) setup. The log is missing.

---

## Comment 13

> Username: jeking3  
> Created_at: 2022-02-28 22:22:34 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-1054720313  

I am re-running, we'll see what happens.

---

## Comment 14

> Username: codecov[bot]  
> Created_at: 2022-02-28 23:19:21 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-1054802672  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/120?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#120](https://codecov.io/gh/boostorg/uuid/pull/120?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c74fc6a) into [develop](https://codecov.io/gh/boostorg/uuid/commit/9fe39a1e129591dea6f530611fcf4300be49d780?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9fe39a1) will **increase** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/120/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/uuid/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #120      +/-   ##  
===========================================  
+ Coverage    60.73%   60.76%   +0.02%       
===========================================  
  Files           34       34                
  Lines         1388     1389       +1       
  Branches       590      590                
===========================================  
+ Hits           843      844       +1       
  Misses          55       55                
  Partials       490      490                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/uuid/detail/random\_provider\_getrandom.ipp](https://codecov.io/gh/boostorg/uuid/pull/120/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb21fcHJvdmlkZXJfZ2V0cmFuZG9tLmlwcA==) | `80.00% <ø> (+1.42%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/120?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/120?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9fe39a1...c74fc6a](https://codecov.io/gh/boostorg/uuid/pull/120?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 15

> Username: jeking3  
> Created_at: 2022-03-21 11:48:07 UTC  
> Url: https://github.com/boostorg/uuid/pull/120#issuecomment-1073798709  

I think we missed the train on getting this into 1.79 but after that, this can be merged for 1.80.

---
