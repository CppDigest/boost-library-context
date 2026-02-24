# #11 Changes required for aarch64 support in boost::context. [Closed]

> Username: ajasty-cavium  
> Created at: 2015-04-16 02:07:03 UTC  
> Updated at: 2015-04-16 07:33:07 UTC  
> Closed at: 2015-04-16 07:33:07 UTC  
> Url: https://github.com/boostorg/context/pull/11  

Request review of changes required for boost::context support on aarch64.

---

## Comment 1

> Username: olk  
> Created_at: 2015-04-16 07:33:07 UTC  
> Url: https://github.com/boostorg/context/pull/11#issuecomment-93665867  

- detail/fcontext_arm.hpp (etc.) are not used anymore (fcotnext_t is obaque pointer)  
- ARM uses the AAPCS calling convention not SYSV (boost.build should already set os.platform to ARM, see https://github.com/boostorg/build/pull/58)

---
