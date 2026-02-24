# #70 - Release Notes for Boost.Uuid Version 1.68.0 [Closed]

> Username: jeking3  
> Created at: 2018-05-08 17:59:04 UTC  
> Updated at: 2018-07-31 15:27:59 UTC  
> Closed at: 2018-07-31 15:27:59 UTC  
> Url: https://github.com/boostorg/uuid/issues/70  

* [phrase library..[@/libs/uuid/ Uuid]:]  
  * [*Breaking change:] sha1 detail namespace header redirection for backwards compatibility was removed ([github_pr uuid 69]).  
  * Added support for std::hash ([github_pr uuid 67]).  
  * Added support for move semantics on random generators ([github_pr uuid 74]).  
  * Properly handle EINTR when acquiring entropy ([github_pr uuid 74]).  
  * Use getrandom(2) instead of getentropy(3) on linux ([github_pr uuid 75]).

---

## Comment 1

> Username: jeking3  
> Created at: 2018-06-17 12:29:15 UTC  
> Url: https://github.com/boostorg/uuid/issues/70#issuecomment-397875611  

PR out: https://github.com/boostorg/website/pull/338
