# #27 minimal changes to allow single move only values to be pushed and poped [Closed]

> Username: tnovotny  
> Created at: 2016-08-02 22:55:10 UTC  
> Updated at: 2017-12-30 09:20:19 UTC  
> Closed at: 2017-12-29 04:51:26 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27  

I have tried to keep the changes minimal.

---

## Comment 1

> Username: tnovotny  
> Created_at: 2016-08-23 11:18:07 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-241700559  

Any feedback would be appreciated.

---

## Comment 2

> Username: tnovotny  
> Created_at: 2017-09-18 17:09:40 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-330289462  

@timblechmann any feedback appreciated.

---

## Comment 3

> Username: timblechmann  
> Created_at: 2017-12-29 04:51:26 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-354397691  

let's focus on #31 (this one breaks c++98 compatibility btw)

---

## Comment 4

> Username: tnovotny  
> Created_at: 2017-12-29 08:48:26 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-354417198  

@timblechmann this should not be closed as this PR enables different things than [#31](https://github.com/boostorg/lockfree/pull/31). I think after over a year of waiting you should invest more time than to just close it. This one is not 'as developed' because it has received NO feedback.

---

## Comment 5

> Username: tnovotny  
> Created_at: 2017-12-29 08:50:57 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-354417468  

> this one breaks c++98 compatibility btw  
  
@timblechmann I don't think that's an argument. Move-able and move-only types added after c++98 really broke lock_free as it does not support them and its been 6 years.

---

## Comment 6

> Username: timblechmann  
> Created_at: 2017-12-29 09:08:44 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-354419235  

@tnovotny some projects are bound to older compilers for various reasons. new boost libraries may choose not to support them, but older libraries should not fail to compile after a library update. if new functionality is added, it will have to be guarded by the corresponding config macros.  
  
if you're improving the PR to make it compatible with c++98 (i'm fine with just not supporting movable types rather than using boost's move emulation), i'm more than happy to merge this PR and close the other one.

---

## Comment 7

> Username: tnovotny  
> Created_at: 2017-12-29 09:32:56 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-354421902  

@timblechmann I'm not a versed boost-developer, so a quick question. Should I use the `BOOST_NO_CXX11_RVALUE_REFERENCES` macro for this or is there a better alternative?

---

## Comment 8

> Username: timblechmann  
> Created_at: 2017-12-29 09:58:05 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-354424722  

@tnovotny yes `BOOST_NO_CXX11_RVALUE_REFERENCES` is probably the way to go. also for type traits, it would be safest to use the boost versions (or introduce a compatibility layer as i've done for the atomic data structures)

---

## Comment 9

> Username: tnovotny  
> Created_at: 2017-12-30 09:19:59 UTC  
> Updated_at: 2017-12-30 09:20:19 UTC  
> Url: https://github.com/boostorg/lockfree/pull/27#issuecomment-354535996  

Since this one was closed I added an updated PR as version as [#41](https://github.com/boostorg/lockfree/pull/41).

---
