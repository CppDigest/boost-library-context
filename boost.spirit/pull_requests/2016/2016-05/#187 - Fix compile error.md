# #187 Fix compile error [Closed]

> Username: phbaer  
> Created at: 2016-05-06 07:07:52 UTC  
> Updated at: 2017-11-07 18:40:34 UTC  
> Closed at: 2017-11-07 18:40:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/187  

This compile issue was introduced in commit e34a955. See also  comment of MarcelRaad (2 Mar 2015) for that commit.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-05-06 09:49:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-217399927  

Aren't those defaulted?

---

## Comment 2

> Username: phbaer  
> Created_at: 2016-05-06 09:55:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-217400954  

Don't think so (https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/support/nonterminal/extract_param.hpp). Just got the compile error on MSVC2015 with Boost 1.60.0.

---

## Comment 3

> Username: mlvdm  
> Created_at: 2016-06-27 16:51:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-228805050  

Just want to say, this also seems to be the case in Qi as shipped in latest stable (and same fix seems to work, except replace karma with qi in the file path and the changed line). Also MSVC2015, if that matters. For sure extract_sig doesn't have default template params, so something needs to be passed.

---

## Comment 4

> Username: djowel  
> Created_at: 2016-06-27 23:15:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-228904106  

What strikes me as odd is that we're not getting these errors on other compilers. Our tests should've complained loudly.

---

## Comment 5

> Username: mlvdm  
> Created_at: 2016-06-28 08:51:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-228990391  

I suspect it's a case of MSVC parsing a bit too aggressively in a non-standard way. Since MSVC is not actually a two-phase compiler, I suspect that during parse it already sees no way that this could work and thus signals compilation issue. A "proper" compiler would only detect this in the second phase (ie, actual instantiation of subrule), which never happens in the code.  
  
For example, if I add just below the `extract_sig` a `static_assert(false, "oops")`, this will fail at the same point (since false is non-dependent). However, if I add a `static_assert(ID_ != ID_, "oops")` it will compile fine (since ID_ is dependent), proving that the template is never actually instantiated. The other hint in the same direction is that the compilation error on `extract_sig` actually doesn't show a "stack" of instantiations to subrule that you would typically see in a template-heavy compiler error message.  
  
For what it's worth, I could also reproduce this with clang/C2, but this may be because it's doing a bunch of MSVCisms as well.  
  
I think the "other" way to work around this is to not include spirit/repository/home/qi.hpp or nonterminal.hpp, (thus avoiding including subrule.hpp) and the problem is also gone. This is what I'm doing currently, as I don't actually need subrule at all.

---

## Comment 6

> Username: MarcelRaad  
> Created_at: 2016-06-28 08:56:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-228991463  

It's the same compile error in the karma_repo_subrule and qi_repo_subrule tests for all compilers, not only MSVC:  
http://www.boost.org/development/tests/develop/developer/spirit-repository.html

---

## Comment 7

> Username: phbaer  
> Created_at: 2016-06-28 09:00:38 UTC  
> Updated_at: 2016-06-28 09:02:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-228992488  

@mlvdm, thanks for looking into that issue as well. Your second workaround sounds good, we also don't use subrules. However, the issue remains.  
  
As @MarcelRaad pointed out, it the tests are actually failing. To be honest, I did not check that state before. Should have included the link in my comment to this pull request.

---

## Comment 8

> Username: djowel  
> Created_at: 2016-06-28 10:05:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-229007159  

OK, can we do a complete PR for both Qi and Karma then?

---

## Comment 9

> Username: Kojoley  
> Created_at: 2016-08-17 20:01:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-240529508  

I think that this PR solves one problem, but adds other bug, because the second parameter for `extract_sig` is an encoding type, but not a local type.  
  
I have tried to come up with the right fix, but I am not sure what it should be. The simplest thing we could done about that is something like this 3879894ca8b2056230dfade89cc25b4deb45adbf (tested with GCC 5.0 and clang 3.8), but maybe encoding support has to be added to the `subrule` like it was done in c5c871c89240bc5937f4c0082da17bb8b73eda68 to the `rule`?

---

## Comment 10

> Username: Kojoley  
> Created_at: 2017-11-07 18:40:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/187#issuecomment-342580446  

Superseded by #259

---
