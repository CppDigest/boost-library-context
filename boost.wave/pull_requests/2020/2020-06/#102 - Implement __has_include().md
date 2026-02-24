# #102 Implement __has_include() [Merged]

> Username: jefftrull  
> Created at: 2020-06-24 21:55:07 UTC  
> Updated at: 2020-06-27 14:22:53 UTC  
> Merged at: 2020-06-27 14:22:53 UTC  
> Closed at: 2020-06-27 14:22:53 UTC  
> Url: https://github.com/boostorg/wave/pull/102  

Implement C++17 feature (and vendor extension, previously) __has_include()  
  
Includes feature flags and unit tests.

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2020-06-24 22:35:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/102#pullrequestreview-437052660  

📁 include/boost/wave/util/cpp_macromap.hpp

```diff
 565 |+ 
 566 |+     // trim leading and trailing blanks
 567 |+     boost::algorithm::trim_if(fn, is_space);
```

> Username: hkaiser  
> Created_at: 2020-06-24 22:35:25 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445209045  

What about (c-style) comments? Is it a good idea to do this on the character level? It might be better to do the trimming on the token level.

> Username: jefftrull  
> Created_at: 2020-06-25 00:15:17 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445239540  

I'm following the pattern [here](https://github.com/boostorg/wave/blob/bacb94f394943f61517406ad0136093bebdc8158/include/boost/wave/util/cpp_iterator.hpp#L1722), where includes are handled by combining the tokens, then trimming. Could there be comments inside a `__has_include()`?

> Username: hkaiser  
> Created_at: 2020-06-25 00:42:08 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445246658  

> I'm following the pattern here,   
  
Should you use the existing `trim_whitespace()` in this case?  
  
> where includes are handled by combining the tokens, then trimming.  
  
Not sure if comments are removed by the parser in this case. I need to check.  
  
> Could there be comments inside a __has_include()?  
  
Sure, why not, it's equivalent to whitespace after all?

> Username: jefftrull  
> Created_at: 2020-06-25 00:49:19 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445248448  

Yeah, so... the weird thing is the existing trim code is in an internal namespace in `cpp_iterator.hpp`, and the macro expansion code is in `cpp_macromap.hpp`. I guess this is a side effect of how `__has_include` crosses the existing boundaries between functionality, combining a little "include processing" and a little "macro processing". I struggled with this for a while. You could say that this approach (a minor wheel reinvention) was the one that I disliked the least :) What would you suggest?  
  
I will check how clang/gcc handle this situation.

> Username: hkaiser  
> Created_at: 2020-06-25 13:35:44 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445562753  

Couldn't we move the helper code to some commonly included spot (for instance, here: https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/macro_helpers.hpp)?

> Username: jefftrull  
> Created_at: 2020-06-25 15:41:53 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445654559  

That sounds like a great idea :) (sorry missed your previous message somehow)

> Username: jefftrull  
> Created_at: 2020-06-25 20:44:26 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445827295  

I can confirm that gcc handles comments within `__has_include()` and Wave... does not, yet. Thanks for the observation.

> Username: jefftrull  
> Created_at: 2020-06-25 22:25:13 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445872167  

Well, that was an easy fix. Comments were removed by the skipper already - I just needed to fix a check I added.

> Username: jefftrull  
> Created_at: 2020-06-25 22:25:48 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r445872368  

Any other concerns at the moment? I'll forge ahead with unit tests in the meantime.


---

## Review 2 [Commented]

> Username: hkaiser  
> Created_at: 2020-06-26 09:08:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/102#pullrequestreview-438136405  

📁 include/boost/wave/grammars/cpp_has_include_grammar.hpp

```diff
  83 |+                         (system_include | nonsystem_include | computed_include)
  84 |+                         )
  85 |+                     >> end_p
```

> Username: hkaiser  
> Created_at: 2020-06-26 09:08:04 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446063462  

What about the right parenthesis? Should this be matched as well?

> Username: hkaiser  
> Created_at: 2020-06-26 09:18:01 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446068459  

nvm, I found where the right parenthesis is handled. Sorry for the noise.

> Username: jefftrull  
> Created_at: 2020-06-26 13:52:09 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446197287  

I'm glad you asked :) I initially did, but then moved it [outside the parser](https://github.com/jefftrull/wave/blob/6dc21c848009b8ffea5471b23a21030f9cbe67fa/include/boost/wave/util/cpp_macromap.hpp#L1812) because of a tricky problem with Classic's greedy matching:  
  
I want to accept expressions including nested parentheses while excluding the final right parenthesis. When I tried to do it with the Kleene star it would always grab every right paren, including the one that terminates `__has_include()` - and it does not backtrack when the full expression `ch_p(T_LEFTPAREN) >> ... >> ch_p(T_RIGHTPAREN)` then fails. The explanation for this problem [here](https://www.boost.org/doc/libs/1_73_0/libs/spirit/classic/doc/rationale.html#exhaustive_rd) did not provide any solution I could understand.  
  
Is there a cleaner way to solve this?

> Username: hkaiser  
> Created_at: 2020-06-26 14:28:21 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446218438  

If you need to accept inner parenthesis a possible solution would be a recursive rule. Something like:  
```  
r = ch_p(T_LEFTPAREN) >> r >> ch_p(T_RIGHTPAREN) | *char_p;  
```  
In any case, I think your current solution is fine as long as the parenthesis match.


---

## Review 3 [Commented]

> Username: hkaiser  
> Created_at: 2020-06-26 09:16:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/102#pullrequestreview-438142166  

📁 include/boost/wave/util/cpp_macromap_utils.hpp

```diff
 144 | 
 145 |-     if ("defined" == name)
 145 |+     if (("defined" == name) || ("__has_include" == name))
```

> Username: hkaiser  
> Created_at: 2020-06-26 09:16:47 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446067776  

Should we check for `__has_include` only if it's detection was enabled (C++20)?

> Username: jefftrull  
> Created_at: 2020-06-26 13:53:26 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446198102  

Yes :) I am actually writing the feature tests (and conditional compilation stuff) right now.  
Also I think this is a C++17 feature - so I'm adding a C++17 mode as well.

> Username: hkaiser  
> Created_at: 2020-06-26 14:29:23 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446219069  

Ok, C++17, then. In this case you might need to adapt the tests to ask for this as well.


---

## Review 4 [Commented]

> Username: hkaiser  
> Created_at: 2020-06-26 09:20:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/102#pullrequestreview-438144482  

📁 test/testwave/testfiles/t_2_027.cpp

```diff
   9 |+ 
  10 |+ // Test error reporting during redefinition of '__has_include'
  11 |+ 
```

> Username: hkaiser  
> Created_at: 2020-06-26 09:20:17 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446069665  

Does this miss a `//O --c++20`?

> Username: jefftrull  
> Created_at: 2020-06-26 13:54:00 UTC  
> Updated_at: 2020-06-27 06:01:57 UTC  
> Url: https://github.com/boostorg/wave/pull/102#discussion_r446198454  

It will be `//O --c++17`... probably sometime later today.


---

## Review 5 [Commented]

> Username: hkaiser  
> Created_at: 2020-06-26 09:21:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/102#pullrequestreview-438145384  

Could you add a test that uses `__has_include()` in a pre-C++20 setting making sure it has no special meaning in this case?

---

## Comment 6

> Username: jefftrull  
> Created_at: 2020-06-26 22:00:14 UTC  
> Url: https://github.com/boostorg/wave/pull/102#issuecomment-650422394  

I'm satisfied with the code at this point and am just waiting for CI to finish. Will wait for any final feedback.

---
