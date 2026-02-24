# #75 Add support for Linux-specific getrandom call to obtain random data. [Merged]

> Username: Lastique  
> Created at: 2018-06-17 16:26:26 UTC  
> Updated at: 2018-10-10 09:41:55 UTC  
> Merged at: 2018-06-18 14:27:41 UTC  
> Closed at: 2018-06-18 14:27:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/75  

getrandom is the base implementation for getentropy on Linux. It is also  
available in the form of a syscall, which can be called directly on systems with  
glibc versions older than 2.25 which don't yet provide wrappers for getrandom or  
getentropy but have a recent enough Linux kernel (for example, Debian Stretch).  
  
On systems other than Linux (e.g. Solaris) getentropy is documented as a  
source for initialization of a user-space PRNG instead of a direct source  
for random data. Since we use the random data directly to initialize UUIDs,  
using getrandom on other platforms, where available, would be more preferable  
than getentropy. Unfortunately, the only other platform that is known to support  
both getentropy and getrandom (Solaris) documents getrandom to return 0  
on error, which is a valid return value on Linux. It's not clear if this is  
a documentation error or a true incompatibility, and I have no way to test,  
so do not use getrandom on Solaris for now. For this reason (and in case  
if it is also used on some other platforms) getentropy backend is still  
preserved.

---

## Review 1 [Commented]

> Username: jeking3  
> Created_at: 2018-06-17 16:54:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/75#pullrequestreview-129394935  

Is the performance difference between getentropy and getrandom so much that it warrants another code path?  
  
In the test Jamfile you may want to add explicit cases that force both of the linux implementations to execute in positive and negative test cases to ensure test coverage.  Without that, most (maybe all) CI builds will test getrandom and nothing will be testing getentropy any more.

📁 include/boost/uuid/detail/random_provider_include_platform.hpp

```diff
  16 | #elif defined(BOOST_UUID_RANDOM_PROVIDER_BCRYPT)
  17 | # include <boost/uuid/detail/random_provider_bcrypt.ipp>
  18 |+ #elif defined(BOOST_UUID_RANDOM_PROVIDER_GETRANDOM)
```

> Username: jeking3  
> Created_at: 2018-06-17 16:48:00 UTC  
> Updated_at: 2018-06-17 18:40:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#discussion_r195934975  

Was trying to keep these in alphabetical order :)


📁 include/boost/uuid/detail/random_provider_getrandom.ipp

```diff
  65 |+ #if defined(BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_IMPL_GETRANDOM)
  66 |+         return BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_IMPL_GETRANDOM(buf, size, flags);
  67 |+ #elif defined(BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_HAS_LIBC_WRAPPER)
```

> Username: jeking3  
> Created_at: 2018-06-17 16:50:23 UTC  
> Updated_at: 2018-06-17 18:40:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#discussion_r195935020  

To implement the unit test could you have the mock implement ::getrandom and have the mock header define BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_HAS_LIBC_WRAPPER?  Unfortunately we're going to have a drop in coverage on the syscall since that (probably) can't be mocked.

> Username: Lastique  
> Created_at: 2018-06-17 17:09:21 UTC  
> Updated_at: 2018-06-17 18:40:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#discussion_r195935540  

I don't like redefining system functions. I've seen you did this with `getentropy` and I deliberately did not follow this route. I think, that mock should be changed as well, but didn't do it in this PR as it was unrelated.

---

📁 include/boost/uuid/detail/random_provider_getrandom.ipp

```diff
  19 |+ #define BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_HAS_LIBC_WRAPPER
  20 |+ #elif defined(__has_include)
  21 |+ #if __has_include(<sys/random.h>)
```

> Username: jeking3  
> Created_at: 2018-06-17 16:53:38 UTC  
> Updated_at: 2018-06-17 18:40:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#discussion_r195935092  

I've never seen __has_include before.  I was just thinking about how nice it would be to have the following include syntax in C++:  
  
```  
#include <boost/uuid/sha1.hpp> or <boost/uuid/detail/sha1.hpp>  
```  
  
To handle headers that move without leaving behind forwarding headers.

> Username: Lastique  
> Created_at: 2018-06-17 17:54:06 UTC  
> Updated_at: 2018-06-17 18:40:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#discussion_r195936618  

>I've never seen __has_include before.  
  
It's one of the SD-6 macros most recent compilers support.  
  
https://en.cppreference.com/w/cpp/experimental/feature_test


---

## Comment 2

> Username: Lastique  
> Created_at: 2018-06-17 17:07:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#issuecomment-397892356  

> Is the performance difference between getentropy and getrandom so much that it warrants another code path?  
  
I don't expect performance to be significantly different. The main reasons for this backend are (a) supporting more platforms (glibc < 2.25) and (b) using the more appropriate API for random data generation. As I noted, `getentropy` is mostly targeted for seeding user-space PRNGs. I.e. read some (not much) random data to initialize a UPRNG and then obtain most of the random data from UPRNG. Given that on Linux `getentropy` is based on `getrandom`, this part is mostly puristic, but still worth mentioning. It may be significant on other systems.  
  
> In the test Jamfile you may want to add explicit cases that force both of the linux implementations to execute in positive and negative test cases to ensure test coverage. Without that, most (maybe all) CI builds will test getrandom and nothing will be testing getentropy any more.  
  
I'm not sure it is worth doing. On Linux, `getentropy` backend is not going to be used, ever, so why test it there. I guess, you probably want to "emulate" other systems by forcing `getentropy`?

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-06-17 17:10:21 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#issuecomment-397892531  

Yes, since we don't have a Solaris CI environment, it's nice to ensure the code doesn't rot somehow.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2018-06-17 18:40:18 UTC  
> Updated_at: 2018-06-18 14:28:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#issuecomment-397897970  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/75?src=pr&el=h1) Report  
> Merging [#75](https://codecov.io/gh/boostorg/uuid/pull/75?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/0d0aa876e5fab6ce80410164403ed4b5ac582645?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/75/graphs/tree.svg?token=6falIr5npV&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/uuid/pull/75?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #75   +/-   ##  
========================================  
  Coverage    78.34%   78.34%             
========================================  
  Files           13       13             
  Lines          605      605             
  Branches       156      156             
========================================  
  Hits           474      474             
  Misses          17       17             
  Partials       114      114  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/75?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/75?src=pr&el=footer). Last update [0d0aa87...6c060f6](https://codecov.io/gh/boostorg/uuid/pull/75?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: Lastique  
> Created_at: 2018-06-17 18:42:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#issuecomment-397898105  

I've updated the PR. For tests, I've added the internal `BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETRANDOM` macro, which is only used by tests on Linux, currently. I've also updated the docs.

---

## Comment 6

> Username: bajizhh  
> Created_at: 2018-10-10 06:23:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#issuecomment-428452256  

**::syscall(SYS_getrandom, buf, size, flags) failed on ubuntu 16.04.1**  
  
throws an exception blow:  
boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::uuids::entropy_error> >  
  
  
system kernel info:  
Linux app2 3.13.0-32-generic #57-Ubuntu SMP Tue Jul 15 03:51:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux

---

## Comment 7

> Username: Lastique  
> Created_at: 2018-10-10 09:41:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/75#issuecomment-428507473  

I've replied in https://github.com/boostorg/uuid/issues/79.

---
