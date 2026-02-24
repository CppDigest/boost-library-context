# #26 - move only types are not supported [Open]

> Username: tnovotny  
> Created at: 2016-08-01 21:15:25 UTC  
> Updated at: 2017-12-29 09:08:52 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26  

It is currently not possible to get a move only type into the lockfree datastructures. It leades to compile errors.

---

## Comment 1

> Username: timblechmann  
> Created at: 2016-08-02 08:28:00 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-236838292  

for some data structures this is by design (the `queue` requires a trivial assignment operator) for the other data structures, patches are more than welcome

---

## Comment 2

> Username: tnovotny  
> Created at: 2016-08-02 09:26:59 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-236851977  

I patched spsc_queue so that it works for me. I posted about this in the boost mailing list here:   
[getting a move only type into spsc_queue](http://lists.boost.org/boost-users/2016/07/86393.php).   
  
In the meantime I forked the repository and have a better implementation that uses std::enable_if. The main "issue" is the "detail\copy_payload.hpp" which is used by serveral implementaions so I'm hesitant to change it.

---

## Comment 3

> Username: timblechmann  
> Created at: 2016-08-02 09:35:58 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-236854087  

i'm not reading the boost-user list. but you could do a PR and we could discuss it there

---

## Comment 4

> Username: tnovotny  
> Created at: 2016-08-02 17:24:47 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-236977482  

I'll tidy the stuff up a little a locally, push it, and then do a PR.

---

## Comment 5

> Username: cor3ntin  
> Created at: 2017-12-28 08:50:10 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-354252945  

@timblechmann any news on that ? Thanks !

---

## Comment 6

> Username: tnovotny  
> Created at: 2017-12-28 23:49:08 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-354375898  

@cor3ntin I made the pull request [minimal changes to allow single move only values to be pushed and poped #27](https://github.com/boostorg/lockfree/pull/27) on Aug 3, 2016 and have received no feedback.

---

## Comment 7

> Username: timblechmann  
> Created at: 2017-12-29 04:50:20 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-354397623  

thanks for the heads-up.  
  
from the 3 PRs #31 looks best and i'm commenting on this. i only have limited time atm to work on this and no real use-case for move semantics, so it would be great if you guys could pick up that pr and complete it

---

## Comment 8

> Username: tnovotny  
> Created at: 2017-12-29 08:37:13 UTC  
> Updated at: 2017-12-29 08:54:05 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-354416003  

@timblechmann But they do different things. This one works with move-only types such as `unique_ptr` where as the other simply does some optimizations for move-able types. [#31](https://github.com/boostorg/lockfree/pull/31) but fails on move-only types.

---

## Comment 9

> Username: timblechmann  
> Created at: 2017-12-29 08:42:50 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-354416592  

#27 will break c++98 compatibility. any PR that will go in should not break compatibility as it may break user code. move-only types should be supported, though.  
  
i don't care too much, which implementation should go in, as long as it's still c++98 compatible, has test coverage and supports both moveable and move-only types :)

---

## Comment 10

> Username: tnovotny  
> Created at: 2017-12-29 09:08:12 UTC  
> Updated at: 2017-12-29 09:08:52 UTC  
> Url: https://github.com/boostorg/lockfree/issues/26#issuecomment-354419192  

@timblechmann I'm sure [#27](https://github.com/boostorg/lockfree/pull/27) can be made c++98 compatible by simply hiding the functionality via `#defines`. We can also do them one after another, first move-able and then move-only.
