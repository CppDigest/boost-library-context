# #76 - Android: <sys/random.h> not available until API 28 [Closed]

> Username: rcdailey  
> Created at: 2018-08-15 16:58:28 UTC  
> Updated at: 2018-08-19 14:36:01 UTC  
> Closed at: 2018-08-19 14:36:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/76  

According to the NDK r17b code, in `sysroot/usr/include/sys/random.h`, it has:  
  
```cpp  
#if __ANDROID_API__ >= 28  
int getentropy(void* __buffer, size_t __buffer_size) __wur __INTRODUCED_IN(28);  
  
ssize_t getrandom(void* __buffer, size_t __buffer_size, unsigned int __flags) __wur __INTRODUCED_IN(28);  
#endif /* __ANDROID_API__ >= 28 */  
```  
  
Looks like the `::getrandom()` function that is used by Boost.UUID in `boost/uuid/detail/random_provider_getrandom.ipp` is not valid to use. At the moment, with no additional configuration, the UUID library cannot be used on Android (My API is set to 15). I think the fix for this would probably be to have some preprocessor logic that checks if we're on Android, and API less than 28, we do not define `BOOST_UUID_RANDOM_PROVIDER_GETRANDOM_HAS_LIBC_WRAPPER`, maybe?

---

## Comment 1

> Username: jeking3  
> Created at: 2018-08-15 17:56:02 UTC  
> Updated at: 2018-08-15 17:56:10 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413280941  

Your assessment is likely correct; unfortunately android is not one of the environments in our standard set of CI builds that we can test.  
  
The selector logic can be found here:  
https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/random_provider_detect_platform.hpp  
  
A workaround for you would be to define `BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX`.  
If you are able to submit a pull request that modifies that file to properly avoid it, for example wrapping the two getentropy/getrandom blocks with something to avoid android if the api is less than 28.  Alternately, perhaps there's a Boost.Predef like way to handle this.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-08-15 18:39:10 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413294207  

I can make a PR in a few days.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-08-15 18:40:36 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413294603  

How can we add android build support to CI?

---

## Comment 4

> Username: Lastique  
> Created at: 2018-08-15 19:12:11 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413303861  

On August 15, 2018 9:40:38 PM "James E. King III"   
<notifications@github.com> wrote:  
  
> How can we add android build support to CI?  
  
I don't think we can, unless we install Android SDK every time tests are   
run. There are Android testers in the official Boost testing table.

---

## Comment 5

> Username: rcdailey  
> Created at: 2018-08-15 19:50:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413314434  

You don't need the Android SDK, just 1 installation of the NDK. The SDK is only needed for Java stuff. You can get away with pure NDK + build system to cross compile native libraries for Android.

---

## Comment 6

> Username: rcdailey  
> Created at: 2018-08-15 19:51:20 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413314675  

Also if your CI pipeline can use docker containers for your build environment, you could use a premade image for the NDK, I think some are available. Again just throwing some thoughts out there, I'm not at all familiar with your environment.

---

## Comment 7

> Username: Lastique  
> Created at: 2018-08-15 20:19:24 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413322571  

On August 15, 2018 10:50:28 PM Robert Dailey <notifications@github.com> wrote:  
  
> You don't need the Android SDK, just 1 installation of the NDK. The SDK is   
> only needed for Java stuff. You can get away with pure NDK + build system   
> to cross compile native libraries for Android.  
  
Doesn't matter. Travis CI and AppVeyor images are not under our control and   
have fixed contents. So the only way we could add Android cross-compilation   
is to download and install NDK every time tests are run. Which is not good   
for testing times and IMHO not worth it.  
  
Maybe it is possible to configure custom images on some paid plan, but that   
is not something we can do locally for Boost.UUID.

---

## Comment 8

> Username: Lastique  
> Created at: 2018-08-15 21:26:55 UTC  
> Url: https://github.com/boostorg/uuid/issues/76#issuecomment-413342903  

Please, see if https://github.com/boostorg/uuid/pull/77 fixes the problem.
