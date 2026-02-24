# #136 Fix to compile with Visual C++ and /Zc:implicitNoexcept-. [Open]

> Username: jaykrell  
> Created at: 2021-10-21 01:00:07 UTC  
> Updated at: 2024-07-13 06:31:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136  

Otherwise gets:  
  Error C2694 'override': overriding virtual function  
  has less restrictive exception specification than base class virtual  
  member function 'base'  
  
Similar changes are being made e.g.:  
  https://github.com/boostorg/json/pull/636  
  
See also, similar from me:  
https://github.com/boostorg/format/pull/85

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-10-21 02:18:02 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-948194725  

This PR doesn't look correct, because it uses `noexcept` and `override` unconditionally. This will cause errors for C++03 and on MSVC versions earlier than 2015 (or 2013, I don't remember which version introduced `noexcept`.)  
  
These need to be `BOOST_NOEXCEPT` and `BOOST_OVERRIDE`, respectively. Perhaps just `BOOST_NOEXCEPT`, because `override` isn't necessary here.

---

## Comment 2

> Username: jaykrell  
> Created_at: 2021-10-21 02:19:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-948195456  

Ok. Serious question then: instead of `= default` should I use `{ }`?  
Or is that going to "too old" compiler?

---

## Comment 3

> Username: jaykrell  
> Created_at: 2021-10-21 02:22:43 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-948196548  

Since you specifically said `C++03` I suspect I should go with `{ }`.

---

## Comment 4

> Username: mclow  
> Created_at: 2021-10-21 02:28:08 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-948198520  

> Since you specifically said `C++03` I suspect I should go with `{ }`.  
  
How about `BOOST_DEFAULTED_FUNCTION`? (see https://www.boost.org/doc/libs/1_77_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_that_allow_use_of_c__11_features_with_c__03_compilers)

---

## Comment 5

> Username: rdoeffinger  
> Created_at: 2022-02-01 12:37:29 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-1026798202  

I think you should squash the 3 commits into a single one, as it's not desirable to have not-quite-correct versions in the history.  
Note: I am not speaking with any particular authority, just making a drive-by comment...

---

## Comment 6

> Username: jaykrell  
> Created_at: 2022-02-02 03:28:41 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-1027541670  

> I think you should squash the 3 commits into a single one, as it's not desirable to have not-quite-correct versions in the history. Note: I am not speaking with any particular authority, just making a drive-by comment...  
  
I agree, but, is it approved othewise? That can be done upon commit in the GitHub gui, including editing the commit message.  
  
Opinions vary and are strong about PR workflow.  
I've seen people, reviewing my code, request strongly never to rebase a PR, as it makes the PR harder to read.  
Even if then squash upon commit is the only choice.

---

## Comment 7

> Username: mclow  
> Created_at: 2022-02-02 04:42:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-1027572012  

This looks OK to me.

---

## Comment 8

> Username: jaykrell  
> Created_at: 2024-07-12 22:17:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-2226427520  

@mclow I squashed and rebased, ok?  
(Is squash really required? A lot of repositories are configured to do that upon commit, not optionally.  
Imho that is 99% the right policy. Unless maybe CI runs and passes at each commit, and users and reviewers agree the history reads ok/better with multiple commits. But I've never seen this.)

---

## Comment 9

> Username: pdimov  
> Created_at: 2024-07-13 06:31:37 UTC  
> Url: https://github.com/boostorg/iostreams/pull/136#issuecomment-2226795052  

Closing/reopening to retrigger CI.

---
