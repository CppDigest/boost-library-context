# #92 - random_provider_base::get_random_bytes throws entropy_error when SYS_getrandom is not available [Closed]

> Username: bgedik  
> Created at: 2019-02-14 12:17:56 UTC  
> Updated at: 2019-04-19 10:44:27 UTC  
> Closed at: 2019-04-19 10:44:27 UTC  
> Url: https://github.com/boostorg/uuid/issues/92  

If you are on a system that does not have a recent glibc, you end up not having `getrandom`. In this case, `random_provider_base::get_random_bytes` calls `get_random`, which in turn calls `::syscall(SYS_getrandom, buf, size, flags)`. If your Linux version is older than 3.17 (where `SYS_getrandom` appeared) and assuming `SYS_getrandom` is not backported to your kernel, the call to `::syscall(SYS_getrandom, buf, size, flags)` returns an error. Unfortunately, this results in throwing an `entropy_error("getrandom")`. This is a bit confusing, as the real problem is that no implementation of `getrandom` was found. It is a different issue to not have enough entropy. It would be preferable to throw a more appropriate exception here.

---

## Comment 1

> Username: patrickdepinguin  
> Created at: 2019-04-02 17:31:38 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479109208  

Actually, the logic to determine the random provider, from boost 1.69.0 is:  
  
```cpp  
#if [windows]  
...  
#elif defined(BOOST_UUID_RANDOM_PROVIDER_HAS_GETRANDOM) && !defined(BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX) && !defined(BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETRANDOM)  
# define BOOST_UUID_RANDOM_PROVIDER_GETRANDOM  
# define BOOST_UUID_RANDOM_PROVIDER_NAME getrandom  
  
#elif BOOST_LIB_C_GNU >= BOOST_VERSION_NUMBER(2, 25, 0) && !defined(BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX)  
# define BOOST_UUID_RANDOM_PROVIDER_GETENTROPY  
# define BOOST_UUID_RANDOM_PROVIDER_NAME getentropy  
  
#else  
# define BOOST_UUID_RANDOM_PROVIDER_POSIX  
# define BOOST_UUID_RANDOM_PROVIDER_NAME posix  
  
#endif  
```  
  
So roughly it is:  
  
* If the 'getrandom' system call is defined in your kernel headers, use it (unless forced not to).  
* Else, if you have glibc >= 2.25, use 'getentropy' (unless forced not to)  
* Else, use posix (/dev/urandom).  
  
So I cannot map your issue description on the above code, although I agree that the exception message is not clear.  
  
I bumped into a similar problem, in the case where glibc is recent (2.28) but the kernel is old (< 3.17). The above random provider selection code will decide to use 'getentropy', which is provided by glibc.  
However,  the getentropy() function internally uses 'getrandom' (http://man7.org/linux/man-pages/man3/getentropy.3.html). If the kernel is too old to provide getrandom, then glibc will return ENOSYS, and boost will throw the entropy exception.  
In my opinion the logic above is flawed: in the case you do not have getrandom, it makes no sense to defer to getentropy. Instead, it should fall back to posix.  
There will never be a case where getrandom is not available but getentropy will work, at least if one can trust the manpage.  
  
I thus think following change should be made:  
  
```diff  
diff --git a/include/boost/uuid/detail/random_provider_detect_platform.hpp b/include/boost/uuid/detail/random_provider_detect_platform.hpp  
index b3b2655..ff5071c 100644  
--- a/include/boost/uuid/detail/random_provider_detect_platform.hpp  
+++ b/include/boost/uuid/detail/random_provider_detect_platform.hpp  
@@ -54,10 +54,6 @@  
 # define BOOST_UUID_RANDOM_PROVIDER_GETRANDOM  
 # define BOOST_UUID_RANDOM_PROVIDER_NAME getrandom  
   
-#elif BOOST_LIB_C_GNU >= BOOST_VERSION_NUMBER(2, 25, 0) && !defined(BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX)  
-# define BOOST_UUID_RANDOM_PROVIDER_GETENTROPY  
-# define BOOST_UUID_RANDOM_PROVIDER_NAME getentropy  
-  
 #else  
 # define BOOST_UUID_RANDOM_PROVIDER_POSIX  
 # define BOOST_UUID_RANDOM_PROVIDER_NAME posix  
```  
(along with the removal of the now unused getentropy provider).

---

## Comment 2

> Username: bgedik  
> Created at: 2019-04-02 17:53:22 UTC  
> Updated at: 2019-04-02 17:55:05 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479119220  

@patrickdepinguin  Please take a look at here: https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/random_provider_getrandom.ipp  
  
I don’t see BOOST_UUID_RANDOM_PROVIDER_NAME being used there.  
  
I was hitting line 70 in this file.

---

## Comment 3

> Username: patrickdepinguin  
> Created at: 2019-04-02 18:00:34 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479122560  

The selection of the provider is here, it seems:  
https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/random_provider_include_platform.hpp  
  
This selection is based on the BOOST_UUID_RANDOM_PROVIDER_xxxxx macros, which are defined via this file: https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/random_provider_detect_platform.hpp  
  
What were the glibc and kernel header versions you were using?

---

## Comment 4

> Username: jeking3  
> Created at: 2019-04-02 18:19:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479131049  

Unfortunately the addition of getentropy has been problematic.  
  
Most of the errors seem to be around misaligned kernel headers.  See https://sourceware.org/glibc/wiki/FAQ#What_version_of_the_Linux_kernel_headers_should_be_used  
  
In this case, glibc was likely compiled against kernel headers that have this system call, and then the library was built and moved to another system with an older kernel?  Otherwise the test logic wrong, in that it depends on the glibc version and not the glibc + kernel headers version that has the syscall.  
  
That said, given the number of issues people have run into with this, we may end up removing it in 1.71.  
I'm not sure mow much more expensive getrandom is over getentropy; I assume that was the original motivation for the addition.  
    
cc: @Lastique

---

## Comment 5

> Username: patrickdepinguin  
> Created at: 2019-04-02 18:43:04 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479141638  

Using kernel headers that are newer than your running kernel could indeed explain the problem of @bgedik.  
  
The glibc manpage for getentropy claims that getentropy uses getrandom internally. So however expensive getrandom is, getentropy will be at least as expensive. That is my argument: getentropy cannot bring additional advantage: it can only work if getrandom is there, and in that case the provider was already decided in the previous 'elif'.  
  
Removing getentropy solves my use case, but will not solve that of @bgedik, since he falls in the 'getrandom' case.

---

## Comment 6

> Username: bgedik  
> Created at: 2019-04-02 18:52:39 UTC  
> Updated at: 2019-04-02 20:08:39 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479145717  

Nice to see you here James.  
  
@patrickdepinguin: Indeed what happened was we had our code compiled with kernel 3.10.0-957.1.3.el7.x86_64 and later run it with 3.10.0-327.4.4.el7.x86_64.  
  
What I would like to see changed minimally is the exception, as it blindly throws an entropy error, while in reality, the syscall failed.  
  
Also, one can look at the errno after the syscall and use an alternative if getrandom is not available as a syscall (a runtime plan B, maybe reading from /dev/urandom directly?).

---

## Comment 7

> Username: thehcma  
> Created at: 2019-04-02 18:52:50 UTC  
> Updated at: 2019-04-02 18:53:30 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479145805  

In our case (I work with @bgedik), we use (stock) RHEL7 in our development environment, at some point in the recent past the following combination was being distributed by RedHat:  
  
on RHEL 7.2.1511, where we hit the problem, the following versions were being used:  
  
kernel: 3.10.0-327.4.4.el7.x86_64  
glibc: glibc 2.17  
  
This is where we hit the problem.  
  
Currently, RedHat distributes RHEL7.6, where the issue is no longer present (since it now includes a new kernel, which properly implements ``SYS_getrandom``).

---

## Comment 8

> Username: Lastique  
> Created at: 2019-04-03 07:54:51 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-479380506  

@jeking3 You are the maintainer, of course, but I would be opposed to getrandom removal. In no case it makes sense to use getentropy when getrandom is available. The former function is intended to initialize user's PRNG and not used as the primary source of random bytes. The latter *is* the PRNG that gets initialized from the entropy pool internally. On Linux, the two functions are implemented the same way, but that is nothing more than implementation detail, on which I don't want to rely. On other systems they may be implemented differently.  
  
All problems that were brought up about this so far that I'm aware of are user's or package maintainers' misconfiguration. If there is a way to better detect such misconfigurations then I'm all for improving Boost.UUID in this regard. But I'm strongly opposed to removing or crippling Boost.UUID in attempt to work around these errors. Boost.UUID is not where the fix is due. If better detection is not possible then add a section in the docs about the problem and how users can deal with it and keep the code.  
  
Regarding @patrickdepinguin analysis of the backend selection. I believe, there are BSD versions that do not have getrandom but have getentropy. Admittedly, I have no experience with BSD, and I'm not sure if glibc can be the libc in such cases, but I didn't want to deny the possibility. I suppose, the selection logic can be tweaked to explicitly disable getentropy *on Linux*, when we know getrandom is not available. I.e. disable something *we know* won't work instead of something that *may* work. I can prepare a PR for that.

---

## Comment 9

> Username: jeking3  
> Created at: 2019-04-08 11:06:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-480786857  

@thehcma, with kernel 3.10 and glibc 2.17 it should not be selecting getentropy.  The criteria for selecting it is BOOST_LIB_C_GNU >= BOOST_VERSION_NUMBER(2, 25, 0).  Could you throw in some stringified macros and print out the versions of these two items on that system?  It sounds like we're not detecting the glibc version properly.

---

## Comment 10

> Username: patrickdepinguin  
> Created at: 2019-04-08 12:14:51 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-480806613  

@jeking3 From what I understand from the posts of @thehcma and @bgedik they fall in the 'getrandom' case, which is triggered based on kernel version. Normally the check should come down to '>= 3.17' but perhaps Redhat backported the getrandom call to their 3.10 version.  
  
It is me who falls in the getentropy call. Perhaps I caused too much confusion by adding onto this same issue, but I felt the original issue was very related.

---

## Comment 11

> Username: bgedik  
> Created at: 2019-04-08 13:15:01 UTC  
> Updated at: 2019-04-08 13:16:16 UTC  
> Url: https://github.com/boostorg/uuid/issues/92#issuecomment-480826628  

It is as @patrickdepinguin guessed. RedHat backported the system call to 3.10.0-957.1.3.el7.x86_64 but not  to 3.10.0-327.4.4.el7.x86_64. As such, when running on 3.10.0-327.4.4.el7.x86_64 (using a binary that was compiled on 3.10.0-957.1.3.el7.x86_64), we get an error from the system call, as no such system call exists. But due to the gaps in error checking, this results in an `entropy_error` exception.
