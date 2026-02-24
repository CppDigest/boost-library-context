# #121 document parsing functions [Closed]

> Username: alandefreitas  
> Created at: 2022-02-10 23:17:11 UTC  
> Updated at: 2022-02-17 16:28:40 UTC  
> Closed at: 2022-02-17 16:28:40 UTC  
> Url: https://github.com/boostorg/url/pull/121  



---

## Review 1 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-10 23:19:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-879637168  

📁 include/boost/url/impl/segments_view.ipp

```diff
 214 |-         os << '/';
 215 |-     skip:
 216 |-         auto s(*it++);
```

> Username: alandefreitas  
> Created_at: 2022-02-10 23:19:51 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r804227805  

This was a segmentation fault when `vw.is_absolute()` was false and the path had 0 segments because we would go to `skip` without checking if `it != end`.

> Username: vinniefalco  
> Created_at: 2022-02-11 00:07:41 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r804255117  

it needs a test

> Username: alandefreitas  
> Created_at: 2022-02-11 00:31:28 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r804264446  

Yes. I included a test.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-02-10 23:24:41 UTC  
> Url: https://github.com/boostorg/url/pull/121#issuecomment-1035636476  

An automated preview of the documentation is available at [https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-11 00:08:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-879663951  

📁 include/boost/url/params.hpp

```diff
  77 |-         The @ref params underlying allocator is used to construct the instances
  78 |-         of @ref params::reference.
  73 |+         Memory for allocated strings uses the allocator specified on creation
```

> Username: vinniefalco  
> Created_at: 2022-02-11 00:08:38 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r804255492  

we say construction not creation


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-02-11 01:14:42 UTC  
> Url: https://github.com/boostorg/url/pull/121#issuecomment-1035697573  

An automated preview of the documentation is available at [https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-02-11 04:08:55 UTC  
> Updated_at: 2022-02-17 09:56:07 UTC  
> Url: https://github.com/boostorg/url/pull/121#issuecomment-1035867349  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/121?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#121](https://codecov.io/gh/CPPAlliance/url/pull/121?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (22e0d6e) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/8911d05c35123f978e90321fea863385b63525d4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (8911d05) will **increase** coverage by `0.16%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/121/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/121?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #121      +/-   ##  
===========================================  
+ Coverage    96.22%   96.39%   +0.16%       
===========================================  
  Files          104      104                
  Lines         5486     5485       -1       
===========================================  
+ Hits          5279     5287       +8       
+ Misses         207      198       -9       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/121?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/121/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvY29uc3Rfc3RyaW5nLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/segments\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/121/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c192aWV3LmlwcA==) | `96.70% <100.00%> (-0.04%)` | :arrow_down: |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/121/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `96.27% <0.00%> (+3.10%)` | :arrow_up: |  
| [include/boost/url/impl/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/121/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaHBw) | `98.48% <0.00%> (+6.06%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/121?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/121?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [8911d05...22e0d6e](https://codecov.io/gh/CPPAlliance/url/pull/121?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-02-11 16:17:26 UTC  
> Url: https://github.com/boostorg/url/pull/121#issuecomment-1036378827  

@glenfe what do you think?

---

## Review 7 [Commented]

> Username: cmazakas  
> Created_at: 2022-02-13 16:27:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-880996821  

📁 doc/qbk/3.0.parsing.qbk

```diff
 130 |+ report errors without recurring to exceptions.
 131 |+ 
 132 |+ The functions `result::has_value` and `result::has_value` can be used to
```

> Username: cmazakas  
> Created_at: 2022-02-13 15:37:20 UTC  
> Updated_at: 2022-02-13 16:27:15 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r805352059  

We list `result::has_value` twice which I'm assuming is a typo.

---

📁 doc/qbk/3.0.parsing.qbk

```diff
 128 |+ In many places, functions in the library have a return type which uses the
 129 |+ __result__ alias template. This class allows the parsing algorithms to
 130 |+ report errors without recurring to exceptions.
```

> Username: cmazakas  
> Created_at: 2022-02-13 15:38:19 UTC  
> Updated_at: 2022-02-13 16:27:15 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r805352239  

I'm assuming "recurring to exceptions" was meant to be "referring to exceptions".

---

📁 doc/qbk/3.0.parsing.qbk

```diff
 141 |+ 
 142 |+ Check the reference for __result__ for a synopsis of the type. For complete
 143 |+ information please consult the full `result` documentation in Boost.System.
```

> Username: cmazakas  
> Created_at: 2022-02-13 15:39:37 UTC  
> Updated_at: 2022-02-13 16:27:15 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r805352465  

If possible, we should cross-reference to the actual System docs here so users can just follow a link.


📁 doc/qbk/3.1.scheme.qbk

```diff
  46 |+ 
  47 |+ A number of schemes are used to define the semantics of URLs. For instance,
  48 |+ the term web address if often used informally to describe URLs with the
```

> Username: cmazakas  
> Created_at: 2022-02-13 15:42:17 UTC  
> Updated_at: 2022-02-13 16:27:15 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r805352851  

"if often" should be "is often"

---

📁 doc/qbk/3.1.scheme.qbk

```diff
 163 |+ If the URL has no scheme, this function returns an empty string. To check whether
 164 |+ a URL contains a scheme the function
 165 |+ `__url_view__::[link url.ref.boost__urls__url_view.has_scheme has_scheme]` might be used.
```

> Username: cmazakas  
> Created_at: 2022-02-13 15:46:29 UTC  
> Updated_at: 2022-02-13 16:27:15 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r805353421  

This `qbk` seems to appear in the rendered HTML.


📁 doc/qbk/3.2.authority.qbk

```diff
 321 |+ ]]]
 322 |+ 
 323 |+ Although this is no mandatory, note that the encoded host is rarely
```

> Username: cmazakas  
> Created_at: 2022-02-13 15:54:04 UTC  
> Updated_at: 2022-02-13 16:27:15 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r805354408  

This should be "not mandatory" instead of "no mandatory"

---

📁 doc/qbk/3.2.authority.qbk

```diff
 381 |+ the password component should be used with care.
 382 |+ 
 383 |+ It is usually not recommendable to transfer password data through URLs
```

> Username: cmazakas  
> Created_at: 2022-02-13 16:02:00 UTC  
> Updated_at: 2022-02-13 16:27:15 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r805357464  

I would use stronger wording and outright say "It is not recommended to transfer password data..."


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-02-14 23:09:37 UTC  
> Url: https://github.com/boostorg/url/pull/121#issuecomment-1039665156  

An automated preview of the documentation is available at [https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-16 15:51:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-884751246  

📁 example/snippets.cpp

```diff
 313 |+ 
 314 |+ void
 315 |+ parsing_authority() {
```

> Username: vinniefalco  
> Created_at: 2022-02-16 15:51:18 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808150104  

Why can't we get the braces right?

> Username: alandefreitas  
> Created_at: 2022-02-16 19:16:35 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808371159  

I had got 89/95 braces right this time :D


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-16 15:52:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-884753841  

📁 example/snippets.cpp

```diff
   5 |+ // file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   6 |+ //
   7 |+ // Official repository: https://github.com/CppAlliance/url
```

> Username: vinniefalco  
> Created_at: 2022-02-16 15:52:47 UTC  
> Updated_at: 2022-02-16 15:52:48 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808152195  

The capitalization is not quite right on the URL, which might cause it to be skipped when we do a find and replace.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-16 15:52:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-884753967  

📁 example/snippets.cpp

```diff
   9 |+ 
  10 |+ //[snippet_headers_1
  11 |+ #include <boost/url.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-02-16 15:52:51 UTC  
> Updated_at: 2022-02-16 15:52:52 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808152292  

This file should not be in examples/ because it doesn't show best practices (such as `using boost::urls`). It should be in test/


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-16 15:53:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-884755264  

📁 example/snippets.cpp

```diff
  14 |+ #if 0
  15 |+ //[snippet_headers_2
  16 |+ #include <boost/url/src.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-02-16 15:53:43 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808153334  

Well, come on now - if you are going to put `#if 0` around these statements, then they aren't compiled. If they aren't compiled, then why bother to put them in the .cpp file? You might as well just put the statement in a C++ code block in the qbk file :)

> Username: alandefreitas  
> Created_at: 2022-02-16 19:17:04 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808371654  

Yes. We can't get the compilation test in this case. My rationale was that it would much easier to remember to update snippet_headers_1 and snippet_headers_2 together in case anything needs to change.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-16 16:00:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-884765317  

📁 include/boost/url/const_string.hpp

```diff
  51 |-         dynamic allocation.
  52 |-     */
  46 |+ private:
```

> Username: vinniefalco  
> Created_at: 2022-02-16 16:00:13 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808162084  

prefer typing less rather than more. `class` is private by default.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-16 16:01:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-884766567  

📁 include/boost/url/impl/segments_view.ipp

```diff
 210 |-     if(! vw.is_absolute())
 211 |-         goto skip;
 210 |+     if(! vw.is_absolute() && it != end) {
```

> Username: vinniefalco  
> Created_at: 2022-02-16 16:01:03 UTC  
> Updated_at: 2022-02-16 16:01:04 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808163060  

This has to be split onto two lines for coverage  
```  
if( ! vw.is_absolute() &&  
    it != end)  
```  
  
Don't need braces for a single statement


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-16 16:13:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/121#pullrequestreview-884785460  

📁 include/boost/url/impl/segments_view.ipp

```diff
 216 |-         auto s(*it++);
 217 |-         os << s;
 215 |+         os << '/' << *it++;
```

> Username: vinniefalco  
> Created_at: 2022-02-16 16:13:50 UTC  
> Url: https://github.com/boostorg/url/pull/121#discussion_r808179310  

`it != end` is being checked twice. This could be written as  
```  
if( it != end )  
{  
    if( vw.is_absolute() )  
        os << "/";  
    os << *it;  
    while( ++it != end )  
        os << '/' << *it;  
}  
```


---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-02-16 19:24:48 UTC  
> Url: https://github.com/boostorg/url/pull/121#issuecomment-1042075761  

An automated preview of the documentation is available at [https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-02-17 05:24:39 UTC  
> Url: https://github.com/boostorg/url/pull/121#issuecomment-1042587958  

An automated preview of the documentation is available at [https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://121.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
