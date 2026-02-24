# #330 Enable futex support in macOS 14.4+ through os_sync_wait_on_address [Open]

> Username: hpnkv  
> Created at: 2025-04-27 18:39:26 UTC  
> Updated at: 2025-04-27 19:06:07 UTC  
> Url: https://github.com/boostorg/fiber/pull/330  

Hi! Starting with version 14.4, macOS SDK exposes a header that allows to operate with futexes: https://developer.apple.com/documentation/os/os_sync_wait_on_address.  
  
I made a quick change, and it seems to work in my application: with the content of this PR, by defining `BOOST_FIBERS_SPINLOCK_TTAS_FUTEX` and an appropriate `CMAKE_OSX_DEPLOYMENT_TARGET`, I am able to use futex-based synchronisation primitives.  
  
On versions prior to 14.4, the code should in principle default to its usual behaviour (pure spinlocks), so this should be a pretty self-contained change. Tests run with `b2 cflags=-mmacosx-version-min=14.4 cxxflags=-mmacosx-version-min=14.4` pass, however, I am willing to provide something else for due process, if you have something on your mind.

---
