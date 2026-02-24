# #119 Expand documentation [Closed]

> Username: alandefreitas  
> Created at: 2022-02-02 21:29:32 UTC  
> Updated at: 2022-02-05 21:21:22 UTC  
> Closed at: 2022-02-05 05:41:41 UTC  
> Url: https://github.com/boostorg/url/pull/119  



---

## Review 1 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:30:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871128727  

📁 CMakeLists.txt

```diff
  43 |-     add_subdirectory(../.. _deps/boost EXCLUDE_FROM_ALL)
  48 |+     if (BOOST_URL_FIND_PACKAGE_BOOST)
  49 |+         find_package(Boost 1.78.0 REQUIRED)
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:30:26 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798028583  

This makes it possible to test the library locally with containers. I can keep this local if you want.


---

## Review 2 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:31:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871129434  

📁 doc/qbk/1.0.overview.qbk

```diff
  21 | this is a valid URL which satisfies the ['absolute-URI] grammar:
  22 |+ 
  23 |+ [teletype]
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:31:11 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798029074  

teletype means what's coming is plain text and it should try to highlight it as C++


---

## Review 3 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:31:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871129908  

📁 doc/qbk/1.0.overview.qbk

```diff
  26 | ```
  27 | 
  28 |+ [/ Introduction to URL ]
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:31:41 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798029421  

I attempted to replicate the patterns I identified in Boost.Json here.


---

## Review 4 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:32:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871130603  

📁 doc/qbk/1.0.overview.qbk

```diff
 113 | [section:security_review Security Review (Bishop Fox)]
  98 |- TBA
 114 |+ To be announced
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:32:28 UTC  
> Updated_at: 2022-02-02 21:32:29 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798029974  

I changed this because everyone whose first language is not English will need to google it.

> Username: vinniefalco  
> Created_at: 2022-02-05 21:21:22 UTC  
> Updated_at: 2022-02-05 21:21:23 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r800101206  

LOL.... well, by the time this gets into boost it will not be TBA.


---

## Review 5 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:33:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871131755  

📁 doc/qbk/1.0.overview.qbk

```diff
 118 | 
 103 |- [h1 Credits]
 119 |+ [h1 Acknowledgments]
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:33:42 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798030788  

I changed this one because Credits might imply the authors while Acknowledgments imply people and institutions who helped a lot. If this gets into Boost, it might be worth mentioning the alliance in general here.


---

## Review 6 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:34:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871132364  

📁 doc/qbk/2.0.basic_usage.qbk

```diff
   9 | 
  10 |- [section Preface]
  10 |+ [section Basic Usage]
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:34:23 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798031197  

Preface seemed vague. The file is also called usage.


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-02-02 21:34:39 UTC  
> Url: https://github.com/boostorg/url/pull/119#issuecomment-1028379137  

An automated preview of the documentation is available at [https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 8 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:35:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871133041  

📁 doc/qbk/4.0.Modification.qbk

```diff
  10 |+ 
  11 |+ 
  12 | A URL string can be parsed using one of the parsing functions.
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:35:07 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798031696  

Should this content be merged with the rest of the documentation?


---

## Review 9 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:35:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871133692  

📁 example/CMakeLists.txt

```diff
  14 |+ #
  15 |+ 
  16 |+ add_executable(quicklook
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:35:51 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798032161  

This example helped me identify some bugs in the documentation examples. Even the first `parse_uri` example wasn't working before.


---

## Review 10 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:36:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871134458  

📁 example/quicklook.cpp

```diff
  16 |+ main(int, char**)
  17 |+ {
  18 |+     result<url_view> r = parse_uri( "https://user:pass@www.example.com:443/path/to/my%2dfile.txt?id=42&name=John%20Doe#page%20anchor" );
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:36:39 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798032644  

This contains all snippets we present in 2.0.quicklook.qkb. You probably want to organize this differently though.


---

## Review 11 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:38:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871136437  

📁 include/boost/url/const_string.hpp

```diff
 266 | 
 266 |-         @param size The size of the resulting string
 267 |+         @param n The size of the resulting string
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:38:49 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798034108  

Some small quickbook / docca warnings we were getting because @param didn't match the parameter name.  
  
We also have a warning  
  
```  
bin.v2/libs/url/doc/reference.qbk:339: warning: line breaks generate invalid boostbook (will only note first occurrence).  
```  
  
but I think there's an issue in docca for that already.  
  
Another warning seems to be related to quickbook and these are all the warnings left.  
  
```  
 inlined from ‘void quickbook::quickbook_grammar::impl::init_phrase_elements()’ at boost/tools/quickbook/src/phrase_element_grammar.cpp:41:65:  
boost/tools/quickbook/src/actions.hpp:184:12: warning: ‘<unnamed>.quickbook::cond_phrase_push::saved_conditional’ may be used uninitialized [-Wmaybe-uninitialized]  
  184 |     struct cond_phrase_push : scoped_action_base  
      |            ^~~~~~~~~~~~~~~~  
--  
Generating output file: bin.v2/libs/url/doc/url_doc.xml  
```

> Username: vinniefalco  
> Created_at: 2022-02-02 22:18:15 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798059848  

That's a compilation warning when building quickbook itself.


---

## Review 12 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:39:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871137053  

📁 test/unit/grammar/lut_chars.cpp

```diff
  53 | 
  54 |             constexpr lut_chars alnum_chars_ = alpha_chars_ + "0123456789";
  55 |+             (void)alnum_chars_;
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:39:27 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798034555  

That generated a warning in GCC (unused variable).


---

## Review 13 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:40:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871138145  

📁 test/unit/url.cpp

```diff
 642 |     void
 643 |-     testHost()
 643 |+     set(string_view s1,
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:40:40 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798035381  

As with other PRs. This fixes warnings related to default parameters in lambdas.  
  
The warning:  
  
```  
warning: default argument specified for lambda parameter [-pedantic]  
```  
  
The reason is in Section 5.1.2 paragraph 5.  
  
> Default arguments (8.3.6) shall not be specified in the parameter-declaration-clause of a lambda-declarator.


---

## Review 14 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:41:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871138543  

📁 test/unit/url_view.cpp

```diff
 171 |+     testScheme()
 172 |+     {
 173 |+         good_scheme("http://", "http", scheme::http);
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:41:07 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798035652  

The name `good` was already taken here.


---

## Review 15 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:47:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871143930  

📁 doc/qbk/2.0.quicklook.qbk

```diff
 133 |+ 
 134 |+ [note
 135 |+     __url_view__ does not provide decoded functions for compound elements, such as
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:47:11 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798039512  

This is weird though. The only element for which delimiters might not be ambiguous is `fragment` because it's the last one and usually not compound. The `url::query` example shows that clearly. We lose symmetry because some parts are being removed for ambiguity and some are not.  
  
However, if we remove all the ambiguous ones, we are only left with the `encoded_` versions because all other decoded versions are ambiguous, except `fragment`. This is a problem because we have two options: (i) the `encoded_` prefix becomes redundant because it's all encoded, or (ii) the decoded versions need to be dedicated to the views, in which case and we would need to reformulate a few things.

> Username: vinniefalco  
> Created_at: 2022-02-02 22:14:24 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798057368  

I don't think this is a problem


---

## Review 16 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 21:47:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871144689  

📁 doc/qbk/2.0.quicklook.qbk

```diff
 359 |+ ```
 360 |+     params p = u.params();
 361 |+     p.emplace_at(p.find("name"), "name", "Vinnie Falco");
```

> Username: alandefreitas  
> Created_at: 2022-02-02 21:47:59 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798040073  

I'm not sure this is the intended usage, but the usual `operator*` and `it->value` don't work for this case.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-02 22:10:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871165281  

📁 doc/qbk/2.0.quicklook.qbk

```diff
   9 |+ ]
  10 |+ 
  11 |+ [section:quick_look Quick Look]
```

> Username: vinniefalco  
> Created_at: 2022-02-02 22:10:45 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798055155  

don't give section names if you don't have to, e.g.  
```  
[section Quick Look]  
```


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-02 22:54:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871199595  

📁 include/boost/url/params.hpp

```diff
  73 |         This iterator lazily constructs instances of @ref params::reference, which
  74 |-         contain @ref string_value decoded representations of the current <key, value> pair
  74 |+         contain @ref const_string decoded representations of the current <key, value> pair
```

> Username: vinniefalco  
> Created_at: 2022-02-02 22:54:27 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798080997  

You have to be careful putting `<` and `>` in javadocs. It is usually better not to do that, because it will confuse the toolchain.


---

## Review 19 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-02 23:47:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-871233287  

📁 example/quicklook.cpp

```diff
  83 |+     //[snippet_decoding_1
  84 |+     std::cout <<
  85 |+         "query    : " << u.query()            << '\n' <<
```

> Username: alandefreitas  
> Created_at: 2022-02-02 23:47:09 UTC  
> Updated_at: 2022-02-02 23:47:45 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r798106873  

All these functions using const_string give me two warnings from static analysis.   
  
```  
Slicing object from type 'const_string' to 'basic_string_view<char>' discards 32 bytes of state  
```  
  
and   
  
```  
Use pointer or reference to avoid slicing from "const_string" to "basic_string_view<char>".  
```  
  
I've been ignoring these so far because I don't see a simple and non-controversial solution.


---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-02-02 23:52:52 UTC  
> Url: https://github.com/boostorg/url/pull/119#issuecomment-1028467544  

An automated preview of the documentation is available at [https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-02-02 23:59:38 UTC  
> Url: https://github.com/boostorg/url/pull/119#issuecomment-1028470586  

An automated preview of the documentation is available at [https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 22

> Username: codecov[bot]  
> Created_at: 2022-02-03 05:40:53 UTC  
> Updated_at: 2022-02-04 03:40:46 UTC  
> Url: https://github.com/boostorg/url/pull/119#issuecomment-1028621956  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/119?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#119](https://codecov.io/gh/CPPAlliance/url/pull/119?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (1ca1f64) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/8911d05c35123f978e90321fea863385b63525d4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (8911d05) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 1ca1f64 differs from pull request most recent head 96bed36. Consider uploading reports for the commit 96bed36 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/119/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/119?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #119   +/-   ##  
========================================  
  Coverage    96.22%   96.22%             
========================================  
  Files          104      104             
  Lines         5486     5486             
========================================  
  Hits          5279     5279             
  Misses         207      207             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/119?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/119?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [8911d05...96bed36](https://codecov.io/gh/CPPAlliance/url/pull/119?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-02-04 03:49:37 UTC  
> Url: https://github.com/boostorg/url/pull/119#issuecomment-1029610564  

An automated preview of the documentation is available at [https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://119.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:31:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873548935  

📁 doc/qbk/2.0.quicklook.qbk

```diff
  17 | scope.
  18 | 
  19 | [c++]
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:31:22 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799791505  

aren't all .cpp snippets in C++ by default?

> Username: alandefreitas  
> Created_at: 2022-02-05 05:01:11 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799964382  

It depends on the snippet that came before. So unless the snippets are close to each other, I often just write `[c++]` so it doesn't depend on what came before in case we change it and forget to check what comes after.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:32:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873549937  

📁 doc/qbk/2.0.quicklook.qbk

```diff
 117 |+ underlying buffer and uses a type-erased allocator.
 118 |+ 
 119 |+ An extra built-in buffer is also provided to avoid allocations for any
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:32:32 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799792255  

I don't think we should advertise this in the public interface. We can just say "const_string" is optimized for the use case of non-modifiable transformed strings, or say nothing.

> Username: vinniefalco  
> Created_at: 2022-02-04 20:33:17 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799792626  

Rationale: if we say this publicly then if we decide to change it, then it would be a breaking change. Not as big as changing a function signature, but still - better not to advertise it.

> Username: alandefreitas  
> Created_at: 2022-02-05 04:55:54 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799960549  

My rationale is that was already part of the API because we made the built-in capacity public. It was just to save some typing in the tests but still. Maybe we should make this private and use 32 directly in the tests.

> Username: vinniefalco  
> Created_at: 2022-02-05 05:42:46 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799993040  

hmm yeah, we should make `builtin_capacity` private.


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:34:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873551474  

📁 doc/qbk/3.0.parsing.qbk

```diff
  87 |+ ]
  88 |+ 
  89 |+ [h5 Functions]
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:34:28 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799793248  

you might just say `[heading Functions]` and let Quickbook auto-number it.

> Username: alandefreitas  
> Created_at: 2022-02-05 04:56:35 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799961005  

No problem. I used `[heading Functions]` first. But changed to `h5` because that's what Boost.Json used.


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:34:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873551777  

📁 doc/qbk/3.0.parsing.qbk

```diff
  82 |- serialized form.
 104 |+ 
 105 |+ [h5 Creating copies]
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:34:49 UTC  
> Updated_at: 2022-02-04 20:34:50 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799793450  

`[heading Copying]`


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:35:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873552245  

📁 doc/qbk/3.0.parsing.qbk

```diff
 134 |+     // an ``__error_code__``.
 135 |+     //
 136 |+     template< class T >
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:35:24 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799793746  

in theory this should be a snippet.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:35:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873552388  

📁 doc/qbk/3.0.parsing.qbk

```diff
 132 |+     // A variant-like object which hold the
 133 |+     // value T upon success, otherwise holds
 134 |+     // an ``__error_code__``.
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:35:35 UTC  
> Updated_at: 2022-02-04 20:35:36 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799793844  

you don't need to link symbols in source code blocks


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:40:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873556069  

📁 doc/qbk/3.0.parsing.qbk

```diff
 109 |- the URL itself. These non-modifying observer operations are described in the
 110 |- sections that follow.
 159 |+ [endsect]
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:40:26 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799796501  

missing carriage return


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:42:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873557500  

📁 example/snippets.cpp

```diff
  28 |+ using_url_views() {
  29 |+     //[snippet_parsing_1
  30 |+     string_view url_str = "https://user:pass@www.example.com:443/path/to/my%2dfile.txt?id=42&name=John%20Doe#page%20anchor";
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:42:10 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799797476  

I prefer `s` over `url_str`, because `url_str` is far too busy, and it looks like a type from the library. How do we know it isn't `urls::url_str`? It would make sense if it was.


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:42:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873557781  

📁 example/snippets.cpp

```diff
  65 |+         else
  66 |+         {
  67 |+             // this happens
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:42:31 UTC  
> Updated_at: 2022-02-04 20:42:32 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799797659  

informal language


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:43:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873558893  

📁 include/boost/url/params.hpp

```diff
  71 |     /** A random-access iterator referencing parameters in a url query.
  72 | 
  73 |         This iterator lazily constructs instances of @ref params::reference, which
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:43:54 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799798453  

you don't need to say that instances are "lazily constructed"


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-04 20:44:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873559409  

📁 include/boost/url/params.hpp

```diff
  75 |         in the underlying @ref url.
  76 | 
  77 |         The @ref params underlying allocator is used to construct the instances
```

> Username: vinniefalco  
> Created_at: 2022-02-04 20:44:34 UTC  
> Updated_at: 2022-02-04 20:44:41 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799798830  

This reads funny. I don't think you should say `@ref params` (does that even work)? Just say that memory for allocated strings uses the allocator specified on creation of the container.


---

## Comment 35

> Username: vinniefalco  
> Created_at: 2022-02-05 05:41:41 UTC  
> Url: https://github.com/boostorg/url/pull/119#issuecomment-1030532538  

Please submit this as a new pull request.

---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 05:43:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873903657  

📁 doc/qbk/3.0.parsing.qbk

```diff
  16 |+ cause confusion. In practice a single algorithm is used for both so
  17 |+ keeping them distinct yields little value. When discussing particular
  18 |+ grammars, the name of the grammar is used exactly as it appears in the
```

> Username: vinniefalco  
> Created_at: 2022-02-05 05:43:46 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799993723  

you have the word "grammar" twice in one sentence


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-05 05:43:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/119#pullrequestreview-873903675  

📁 doc/qbk/3.0.parsing.qbk

```diff
  15 |+ follow the syntax in __rfc3986__. The reason is that the terms URI and IRI
  16 |+ cause confusion. In practice a single algorithm is used for both so
  17 |+ keeping them distinct yields little value. When discussing particular
```

> Username: vinniefalco  
> Created_at: 2022-02-05 05:43:58 UTC  
> Updated_at: 2022-02-05 05:43:59 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799993890  

"yields little value" is subjective

> Username: vinniefalco  
> Created_at: 2022-02-05 05:45:04 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r799994635  

You could say "as the terms URI and IRI and not widely recognized, this library standardizes on the term 'URL' to refer to any string which meets any of the syntactical requirements."

> Username: alandefreitas  
> Created_at: 2022-02-05 21:02:00 UTC  
> Updated_at: 2022-02-05 21:02:01 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r800099635  

Something that looks a little weird from my ignorant perspective is that "this library standardizes on the term 'URL'" but the very next paragraph lists the functions for parsing URLs and none of them use the term `url`. I understand it does that because of the grammar, but it's still weird in this context.   
  
It's like it deviates from the grammar to standardize on "URL", then all functions use the term `uri` not to deviate from the grammar, and then return `url` or `url_view`, deviating from the grammar again. Especially because it doesn't return a `uri_view` or something, to not deviate from the grammar again.   
  
Maybe it would be better to say nothing about standardizing on URL.

> Username: vinniefalco  
> Created_at: 2022-02-05 21:20:38 UTC  
> Updated_at: 2022-02-05 21:20:39 UTC  
> Url: https://github.com/boostorg/url/pull/119#discussion_r800101163  

> Maybe it would be better to say nothing about standardizing on URL.  
  
Yes there is merit to this option


---
