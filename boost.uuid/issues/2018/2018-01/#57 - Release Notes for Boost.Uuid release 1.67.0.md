# #57 - Release Notes for Boost.Uuid release 1.67.0 [Closed]

> Username: jeking3  
> Created at: 2018-01-24 15:10:51 UTC  
> Updated at: 2018-01-28 13:42:46 UTC  
> Closed at: 2018-01-28 13:42:45 UTC  
> Url: https://github.com/boostorg/uuid/issues/57  

* [phrase library..[@/libs/uuid/ Uuid]:]  
  * Optimized random_generator to use OS-provided entropy directly, thus avoiding the initialization cost of a PRNG.  
  * Provided `random_generator_mt19937` for bulk UUID generation (more expensive to initialize the PRNG, but may be faster overall if generating lots of UUIDs).  
  * Handle entropy acquisition errors instead of ignoring them.  
  * Use `getentropy()` or `arc4random()` instead of `/dev/urandom` on platforms that provide it.  
  * Support for CloudABI  
  * Support for Windows UWP (non-desktop partitions) through BCrypt ([github uuid 24])
