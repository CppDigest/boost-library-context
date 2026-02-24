# #165 Don't build `boost_log_setup` library when it isn't necessary.  [Closed]

> Username: Burgch  
> Created at: 2021-10-11 13:04:34 UTC  
> Updated at: 2021-10-24 13:12:55 UTC  
> Closed at: 2021-10-24 13:12:55 UTC  
> Url: https://github.com/boostorg/log/pull/165  

Fixes #164 - this PR uses the presence (or absence) of `BOOST_LOG_WITHOUT_SETTINGS_PARSERS` to switch the `boost_log_setup` target between a an alias or a library.  
  
When that property is defined, the library doesn't actually export any symbols, so it can be replaced by an alias which only defines `BOOST_LOG_SETUP_NO_LIB` to avoid autolinking to a non-existent library.  
  
When `BOOST_LOG_WITHOUT_SETTINGS_PARSERS` is not defined, I believe the behaviour is unchanged. I've only spent a few days with B2, so there may well be better ways to do this, but I couldn't see a way to switch the target type based on properties without using a generator. The generator here is largely inspired by the way the [lib-generator](https://github.com/boostorg/build/blob/develop/src/tools/generators/lib-generator.jam) works.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-10-11 14:19:55 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-940076173  

This seems rather invasive. Wouldn't conditionally adding `<build>no` property to the `lib` target work?

---

## Comment 2

> Username: Burgch  
> Created_at: 2021-10-11 14:40:46 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-940092866  

I wondered about that, and it does seem to work fine for building `boost/log`, but it wasn't clear to me from the B2 documentation what the impact of doing that would be to things downstream of boost/log which include `<library>boost_log_setup` in the requirements of their target(s)?

---

## Comment 3

> Username: Lastique  
> Created_at: 2021-10-11 15:13:34 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-940120170  

If a downstream target depends on `boost_log_setup` and it is disabled at build time, either that downstream target has to adapt (e.g. to remove the dependency on `boost_log_setup` in that configuration) or it has to fail to build (which will happen either way - whether at build system level or during linking).  
  
I believe, your original problem was because Boost installation fails because the linker generated no output when the library was expected. I would hope `<build>no` should solve that, but I haven't tested it.

---

## Comment 4

> Username: Burgch  
> Created_at: 2021-10-11 15:29:56 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-940133067  

Ah, my understanding was that the alias would prevent downstream targets from being broken at the build system level? If a downstream target is actually using functionality from the `boost_log_setup` library, then yes, they'd be broken at link time, but then they're not compatible with a `boost/log` which has been configured with `BOOST_LOG_WITHOUT_SETTINGS_PARSERS`.  
  
My intention was to avoid breaking downstream targets which have blindly added the library dependency on `boost_log_setup` because they've just copied it from the `basic_usage` example, or because they think they need it because they're including `boost/log/utility/setup/common_attributes.hpp`, `boost/log/utility/setup/console.hpp`, or `boost/log/utility/setup/file.hpp`. In these cases, I hoped the `boost_log_setup` alias would act as a header-only library and they wouldn't need to react to this change.

---

## Comment 5

> Username: Lastique  
> Created_at: 2021-10-18 21:17:55 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-946174065  

I tested the `<build>no` approach and it doesn't install neither `boost_log` nor `boost_log_setup` when the latter is disabled. I think this is a [bug in Boost.Build](https://github.com/bfgroup/b2/issues/104).  
  
I don't really want to merge the version you proposed because frankly I don't really understand how it works and I will have a hard time maintaining it. Besides, a number of other libraries are already using `<build>no`, so it looks like this is the intended way.  
  
I have committed my version in 98ebdc9 in case you want to test it, but it is blocked until Boost.Build is fixed.

---

## Comment 6

> Username: Burgch  
> Created_at: 2021-10-21 16:23:21 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-948776084  

I'm not sure if that's a bug in Boost.Build or [boost_install](https://github.com/boostorg/boost_install)?

---

## Comment 7

> Username: Lastique  
> Created_at: 2021-10-22 01:16:06 UTC  
> Updated_at: 2021-10-22 01:16:26 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-949156530  

Given that it reproduces with `stage`, which is implemented by Boost.Build, I think it's Boost.Build.

---

## Comment 8

> Username: Burgch  
> Created_at: 2021-10-22 13:14:59 UTC  
> Url: https://github.com/boostorg/log/pull/165#issuecomment-949621588  

Ah yes, good point!

---
