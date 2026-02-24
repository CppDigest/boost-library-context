# #1231 Experimental: URL parser & tests [Closed]

> Username: coneiric  
> Created at: 2018-08-22 18:55:11 UTC  
> Updated at: 2022-04-05 23:51:10 UTC  
> Closed at: 2020-01-23 14:12:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1231  

Based on work by @Jackarain, an experimental URL parser and unit tests.  
  
Experimental URL (absolute form URI) parser.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-08-22 19:16:40 UTC  
> Updated_at: 2018-11-06 07:47:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-415147728  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1231?src=pr&el=h1) Report  
> Merging [#1231](https://codecov.io/gh/boostorg/beast/pull/1231?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/e7e3fdb808b63c77787c782bdb11b662a45162b7?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1231/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1231?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1231      +/-   ##  
===========================================  
- Coverage    95.36%   95.35%   -0.01%       
===========================================  
  Files          113      113                
  Lines        11117    11117                
===========================================  
- Hits         10602    10601       -1       
- Misses         515      516       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1231?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/impl/buffers\_suffix.ipp](https://codecov.io/gh/boostorg/beast/pull/1231/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19zdWZmaXguaXBw) | `98.8% <0%> (-1.2%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1231?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1231?src=pr&el=footer). Last update [e7e3fdb...eb6f90d](https://codecov.io/gh/boostorg/beast/pull/1231?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:11:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148675007  

📁 include/boost/beast/experimental/core/impl/url_parser.ipp

```diff
  16 |+ // scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]
  17 |+ 
  18 |+ using boost::beast::string_view;
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:11:45 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212111149  

Hmm.. probably better not to have a `using` statement at namespace scope. `string_view` is already a symbol in Beast you should be able to remove this `using` statement and still have everything compile.

> Username: coneiric  
> Created_at: 2018-08-23 01:20:59 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212157966  

Ok, will test it out. Thanks for the tip.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:15:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148676782  

📁 include/boost/beast/experimental/core/impl/url_parser.ipp

```diff
  25 |+ bool url_parser::isunreserved(const char c) const
  26 |+ {
  27 |+     if (std::isalpha(c) || std::isdigit(c) ||
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:15:30 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212112627  

These are slow, they will look at the current locale. Beast has its own private replacements which assume 7-bit ascii (the standard for HTTP and web). Examples:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/string.hpp#L33  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/detail/rfc7230.hpp  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/detail/basic_parser.hpp#L55  
  
Just to keep things interesting, note that if someone uses Beast and sets the current locale differently, then functions like `std::isdigit` can do the wrong thing with respect to RFC compliance. That is why we must avoid `std` char functions.  
  
If you can't find a suitable Beast function you can just add your own as a private interface.  
  
Actually, I have written a lot of these already for some partial work I did on my own URI parser you can copy them:  
https://github.com/vinniefalco/beast/blob/uri/include/boost/beast/uri/impl/parse.ipp#L54

> Username: coneiric  
> Created_at: 2018-08-23 01:29:40 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212159196  

> Actually, I have written a lot of these already for some partial work I did on my own URI parser you can copy them  
  
Awesome, I'll add those as replacements.  
  
I wasn't familiar with the other Beast implementations, thanks for the links.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:16:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148677444  

📁 include/boost/beast/experimental/core/impl/url_parser.ipp

```diff
  57 |+ //--------------------------------------------------------------------------
  58 |+ 
  59 |+ bool url_parser::parse(const std::string& url)
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:16:54 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212113185  

eventually this will need to use error codes but we can ship the first experimental version using plain bool.

> Username: coneiric  
> Created_at: 2018-08-23 01:33:07 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212159587  

Alright, I'll look at other error code implementations in preparation. I thought the bool return was coarse for all the error conditions.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:18:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148677860  

📁 include/boost/beast/experimental/core/impl/url_parser.ipp

```diff
 101 |+             if (c == ':')
 102 |+             {
 103 |+                 scheme_ = make_string_view(part_start, b - part_start - 1);
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:18:05 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212113515  

dunno why we need `make_string_view`, I think this also works:  
```  
scheme_ = { part_start, b - part_start - 1) };  
```

> Username: coneiric  
> Created_at: 2018-08-23 01:34:18 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212159706  

Was curious about the need for that function. I'll remove it, and use your suggestion.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:19:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148678501  

📁 include/boost/beast/experimental/core/impl/url_parser.ipp

```diff
  96 |+             return false;
  97 |+         case scheme:
  98 |+             if (std::isalpha(c) || std::isdigit(c) ||
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:19:47 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212114035  

Does the scheme allow uppercase? I'm not sure. If it does, we probably need to convert it to lower case.

> Username: coneiric  
> Created_at: 2018-08-23 01:36:14 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212159926  

Will check the RFC, and convert if it allows uppercase.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:21:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148679109  

📁 test/beast/experimental/url_parser.cpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2016-2018 Vinnie Falco (vinnie dot falco at gmail dot com)
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:21:32 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212114540  

heh don't put my name on that LOL

> Username: coneiric  
> Created_at: 2018-08-23 01:39:25 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212160283  

:) Right on, will remove it, add mine.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:22:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148679440  

📁 test/beast/experimental/url_parser.cpp

```diff
  69 |+     // Host literal
  70 |+     doParse("a://boost.org", "a", "", "", "boost.org");
  71 |+   }
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:22:38 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212114807  

Nice test vectors!!

> Username: coneiric  
> Created_at: 2018-08-23 01:39:48 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212160335  

Thanks :)


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:22:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148679547  

📁 test/beast/experimental/url_parser.cpp

```diff
  78 |+ };
  79 |+ 
  80 |+ BEAST_DEFINE_TESTSUITE(beast,experimental,url_parser);
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:22:56 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212114891  

The most minor of nits:  
```  
BEAST_DEFINE_TESTSUITE(beast,core,url_parser);  
```

> Username: coneiric  
> Created_at: 2018-08-23 01:40:59 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212160442  

No worries, will change.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:23:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148679885  

📁 test/beast/experimental/url_parser.cpp

```diff
  17 |+ namespace beast {
  18 |+ 
  19 |+ using boost::beast::string_view;
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:23:53 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212115170  

We shouldn't need this

> Username: coneiric  
> Created_at: 2018-08-23 01:41:27 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212160503  

Ok, will remove it.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:24:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148680018  

📁 test/beast/experimental/url_parser.cpp

```diff
  31 |+       string_view fragment = "")
  32 |+   {
  33 |+     boost::beast::url_parser parser;
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:24:27 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212115301  

Dunno if you need to qualify the namespace I think you can write  
```  
url_parser p;  
```

> Username: coneiric  
> Created_at: 2018-08-23 01:42:12 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212160595  

Ok, will try it out.


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:25:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148680438  

📁 include/boost/beast/experimental/core/url_parser.hpp

```diff
   8 |+ //
   9 |+ 
  10 |+ #ifndef BOOST_BEAST_EXPERIMENTAL_CORE_URL_PARSER_HPP
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:25:44 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212115631  

I don't bother mentioning EXPERIMENTAL in the macro:  
```  
#ifndef BOOST_BEAST_CORE_URL_PARSER_HPP  
```  
Code is structured "as-if" it was public, except that it is place in the experimental/ directory. So if we ever decide to publish it, then all that is necessary is to delete "experimental/" from all include paths and apply suitable file renaming.  
  
(Edit: replaced "never" with "ever")

> Username: coneiric  
> Created_at: 2018-08-23 01:43:26 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212160745  

Oh, that makes complete sense, I like it. I'll make the edits.


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:26:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148680637  

📁 include/boost/beast/experimental/core/url_parser.hpp

```diff
  24 |+     struct url_parser
  25 |+     {
  26 |+         string_view scheme_;
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:26:20 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212115782  

MInor point, public members don't end with an underscore, e.g. `scheme` instead of `scheme_`.

> Username: coneiric  
> Created_at: 2018-08-23 01:44:16 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212160840  

Noted, will change.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:26:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148680777  

📁 include/boost/beast/experimental/core/url_parser.hpp

```diff
  47 |+         //--------------------------------------------------------------------------
  48 |+ 
  49 |+         bool parse(const std::string& url);
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:26:45 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212115879  

This parameter should have type `string_view`.

> Username: vinniefalco  
> Created_at: 2018-08-22 21:27:27 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212116077  

Consider a free function instead:  
```  
url_parts parse_url(string_view s);  
```

> Username: coneiric  
> Created_at: 2018-08-23 01:49:10 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212161547  

Ok, I hadn't thought about using a free function.  
  
Guessing `url_parts` will be a wrapper around the public data members?

> Username: vinniefalco  
> Created_at: 2018-08-23 03:03:45 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212170414  

>a wrapper around the public data members  
  
We call that a POD type (plain old data). Anyway, this is something we can experiment with later, it has less priority than the other stuff.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:26:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148680841  

📁 include/boost/beast/experimental/core/url_parser.hpp

```diff
  43 |+         bool ishsegment(const char c) const;
  44 |+ 
  45 |+         bool issubdelims(const char c) const;
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:26:55 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212115940  

These should be `static` and probably not public.

> Username: coneiric  
> Created_at: 2018-08-23 01:49:04 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212161530  

Alright, will make `static`, and change the access/impl.


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:27:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148681056  

📁 include/boost/beast/experimental/core/url_parser.hpp

```diff
  22 |+     using boost::beast::string_view;
  23 |+ 
  24 |+     struct url_parser
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:27:36 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212116108  

This needs a Javadoc comment

> Username: coneiric  
> Created_at: 2018-08-23 01:50:51 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212161751  

Will add one. New to exemplar comments, I'll do my best.

> Username: vinniefalco  
> Created_at: 2018-08-23 03:06:10 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212170685  

The bar for stuff in experimental/ is lower so you could at a minimum get away with just one line:  
```  
/// Parses URLs  
```  
Or  
```  
/** Parses URLs  
*/  
```  
Eventually you will want to add something to the quick reference:  
https://github.com/boostorg/beast/blob/develop/doc/qbk/quickref.xml#L295  
  
No one has ever thought to update the reference but hey, first time for everything I guess! If you don't do it, I will, so no big deal (and setting up the toolchain for building the docs is kind of a hassle so I will understand).


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2018-08-22 21:27:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-148681179  

📁 include/boost/beast/experimental/core/url_parser.hpp

```diff
  12 |+ 
  13 |+ #include <boost/beast/core/string.hpp>
  14 |+ #include <cctype> // for std::isalpha etc...
```

> Username: vinniefalco  
> Created_at: 2018-08-22 21:27:58 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212116220  

`<cctype>` is dangerous, RFC compliant programs should never be using anything from there.

> Username: coneiric  
> Created_at: 2018-08-23 01:53:35 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212162060  

Will remove it, unnecessary with your `isalpha`, `isdigit`, and `ascii_tolower`.

> Username: vinniefalco  
> Created_at: 2018-08-23 03:07:57 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r212170862  

Related: https://github.com/boostorg/beast/issues/1232 (something to think about when this is merged)


---

## Comment 18

> Username: coneiric  
> Created_at: 2018-08-26 23:27:05 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-416080767  

Made the suggested changes, but still working on making `parse_url` better. Need to add the other RFC3986 functions, as well (pchar, etc.).  
  
I forgot to update the Javadoc XML, but will do that in the next set of changes.

---

## Comment 19

> Username: Jackarain  
> Created_at: 2018-08-27 07:13:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-416136349  

good job!

---

## Comment 20

> Username: coneiric  
> Created_at: 2018-08-29 23:30:33 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-417140608  

> good job!  
  
Thanks :)  
  
@vinniefalco I added the remaining RFC3986 functions, error codes, and cleaned up `url_parser.ipp` a bit. Wanted to get it closer to your `uri` branch, but the diffs were getting quite large.  
  
Ran into a problem with docs, after adding this line to `doc/qbk/quickref.xml`:  
```xml  
<member><link linkend="beast.ref.boost__beast__core__uri__url_parser__parse_url">uri::parse_url</link></member>  
```  
I got this error:  
```  
Error: no ID for constraint linkend: beast.ref.boost__beast__core__uri__url_parser__parse_url.  
```  
Tried a number of alternates for `linkend` value with similar results. I didn't find where IDs are defined for the other entries, so this confuses me a lot. Likely due to me being brand new to BoostBook and all.  
  
Any ideas?

---

## Comment 21

> Username: vinniefalco  
> Created_at: 2018-08-30 00:11:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-417148158  

`core` is not a namespace, try `boost__beast__uri__url_parser__parse_url`  
  
Also, you don't have to follow the **uri_parser** branch at all, that is just some exploratory coding and should not be considered canon.

---

## Comment 22

> Username: vinniefalco  
> Created_at: 2018-08-30 00:17:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-417149145  

> Likely due to me being brand new to BoostBook and all.  
  
Actually these ref names are not a function of BoostBook at all but rather the XSL program which transforms the Doxygen XML into Quickbook:  
https://github.com/boostorg/beast/blob/develop/doc/docca/include/docca/doxygen.xsl

---

## Comment 23

> Username: coneiric  
> Created_at: 2018-08-30 01:54:15 UTC  
> Updated_at: 2018-08-30 02:13:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-417163601  

> core is not a namespace, try boost__beast__uri__url_parser__parse_url  
  
Think I may have tried that, but will keep messing with it until it works.  
  
> Also, you don't have to follow the uri_parser branch at all  
  
Alright, I wasn't sure the status of that code, a lot of it is really nice. Will write more of my own code.  
  
> Actually these ref names are not a function of BoostBook  
  
Thanks for the link.

---

## Comment 24

> Username: coneiric  
> Created_at: 2018-09-03 23:32:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-418205303  

@vinniefalco sorry for the entire rewrite, but it was cleaner than trying to do it another way.  
  
I kept the functions for testing if a character `is_[some-set]`, but am not using them. Wasn't sure if you would want to keep them for other uses, and can remove if wanted.  
  
Most of the diff is in `detail/parse.hpp` and the unit tests.  
  
If you would like me to break up the PR into small commits, I can. Thanks for your help on this.  
  
Still no luck on the doc issue, but I'm working on it.

---

## Comment 25

> Username: vinniefalco  
> Created_at: 2018-09-04 00:49:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-418211176  

Rewriting is fine, that is completely your choice. You don't have to break up the pull request, in fact I will squash it down into a single commit. That's usually how I introduce a new feature anyway. It is only later when making bug fixes that I use individual commits, one per corresponding GitHub issue. Unless I am doing a big refactoring, then I will typically squash all the changes down into one before merging.

---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2018-09-04 00:49:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-151885911  

📁 include/boost/beast/experimental/core/uri/buffer.hpp

```diff
  10 |+ #define BOOST_BEAST_CORE_URI_BUFFER_HPP
  11 |+ 
  12 |+ #include <vector>
```

> Username: vinniefalco  
> Created_at: 2018-09-04 00:49:56 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214766060  

standard library includes should come last

> Username: coneiric  
> Created_at: 2018-09-04 03:14:41 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214777967  

Ok, some projects like them first, will move it.

> Username: vinniefalco  
> Created_at: 2018-09-04 04:24:56 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214783718  

Rationale: Compile errors resulting from missing includes or incorrect code are less likely in the standard library headers and more likely in the headers belonging to the library being written. And somewhere in the middle for its dependencies (like other Boost libraries). By including the headers in a particular order, it increases the chances of getting a compiler error from the code being developed. Otherwise, there is a chance that the inclusion of a standard library header first will mask a problem.  
  
This is the order I use:  
  
- Public header files from the same library  
- Private header files from the same library (e.g. detail)  
- Public header files from other libraries  
- Public header files from the standard library


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2018-09-04 00:50:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-151885952  

📁 include/boost/beast/experimental/core/uri/buffer.hpp

```diff
  22 |+   using const_iterator = std::vector<char>::const_iterator;
  23 |+   using value_type = std::vector<char>::value_type;
  24 |+   using size_type = std::vector<char>::size_type;
```

> Username: vinniefalco  
> Created_at: 2018-09-04 00:50:39 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214766097  

Shouldn't these nested types be public?

> Username: coneiric  
> Created_at: 2018-09-04 03:15:25 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214778035  

I didn't see a need to expose them publicly, but I'll move them.


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2018-09-04 00:51:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-151886023  

📁 include/boost/beast/experimental/core/uri/buffer.hpp

```diff
  37 |+   size_type size() const noexcept { return data_.size(); }
  38 |+ 
  39 |+   std::string part_from(const_iterator const start, const_iterator const end) {
```

> Username: vinniefalco  
> Created_at: 2018-09-04 00:51:49 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214766167  

There's no need to mark parameter types passed by value as `const`, in fact it doesn't even change the signature of the function. Also you might want to use the names `first` and `last` so they are unambiguous.

> Username: coneiric  
> Created_at: 2018-09-04 03:16:51 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214778146  

No worries, I can change the names and const-ness. Thanks for the catch.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2018-09-04 00:53:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-151886110  

📁 include/boost/beast/experimental/core/uri/detail/parser.hpp

```diff
  13 |+ #include <vector>
  14 |+ #include <boost/beast/core/string.hpp>
  15 |+ #include <boost/spirit/include/qi.hpp>
```

> Username: vinniefalco  
> Created_at: 2018-09-04 00:53:00 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214766233  

Whoa...!! I don't think this is a good idea.... Spirit is a massive dependency and if we go down this path then it will be all but impossible to make Beast independent of Boost (something which I will need to do with I propose it as a TS).

> Username: coneiric  
> Created_at: 2018-09-04 03:23:50 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214778706  

Really should have asked before doing this then... Oh well, chalk it up as another learning experience.  
  
Writing a parser just seemed a lot cleaner with Spirit, especially for the recursive patterns in `host`/`authority` section. Guess that means a third rewrite...  
  
What do you suggest? Writing the parser with for/while loops and boolean comparison seems to lose out pretty quickly with recursive pattern matching, and hardly seems faster than regex.

> Username: vinniefalco  
> Created_at: 2018-09-04 04:25:49 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214783802  

What is wrong with the original code? It seemed to work and was decent...  
  
If we were to take on Spirit as a dependency there would be an angry mob of Beast users showing up with pitchforks (metaphorically speaking).  
  
Although looking over your qi implementation, it does look pretty darn clean.

> Username: vinniefalco  
> Created_at: 2018-09-04 04:31:19 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214784272  

When considering adding a dependency to a library (like adding Spirit to Beast) it is good to get a second and third opinion and discuss with stakeholders. Keep in mind that there are security issues at play here. The parsers in Beast are the front line of the attack surface. The historical attacks which specifically target URL parsing implementations would fill a thick book. It is harder to provide security assurances because a security audit of Beast which used Spirit would have to include the Spirit source code in the analysis and not only would that become very expensive but then it raises questions about whether each update to Spirit has subsequently introduced a vulnerability in Beast.  
  
My apologies for the efforts you expended making Qi work (it does look very nice). But perhaps we can chalk it up to a learning experience?

> Username: vinniefalco  
> Created_at: 2018-09-04 04:33:00 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214784396  

Examples:  
https://www.blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf  
http://www.cgisecurity.com/lib/URLEmbeddedAttacks.html  
https://www.acunetix.com/blog/whitepaper-http-parameter-pollution/

> Username: coneiric  
> Created_at: 2018-09-04 04:57:36 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214786576  

It makes sense that adding Spirit as a Beast dependency is too heavy handed. Especially from the security audit perspective, so much code to review. I am fine with doing another rewrite, though it would be nice if there was an easy solution here.  
  
The original code was OK, but left a lot to be desired. The giant while loop going over a state machine took me some time to understand, and when I tried pulling it apart with functions, things got difficult. It also read more like C/old-C++ than modern C++.  
  
Thanks for the links on URI/URL related bugs. Will digest those, and hopefully gain some new perspective/ideas on writing this parser.

> Username: vinniefalco  
> Created_at: 2018-09-04 05:04:59 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r214787331  

I would just keep the parser that was there. This is an experimental interface which means it doesn't have to be perfect. It just needs a tidying to make it meet the minimum quality control standards. Future revisions can always include a rewrite of the parser, which will certainly be necessary before graduating to an official (non-experimental) public interface.  
  
As engineers it is natural for us to look at something and want to improve it, of course I do that all the time. But it is also important to do a reasonably accurate cost/benefit analysis. Here,, users are going from nothing (no URL parser) to something (this URL parser). This is practically infinite improvement! There is little to be gained from rewriting it at this stage.  
  
Doing a light tidying lets us get something into the tip of the develop and master branches sooner rather than later, and it lets us see all the pieces on the board together so to speak. We will probably be able to make better informed decisions with something that is at least semi-official in the experimental branch, especially when users pick it up and start providing feedback. This will let us work smarter instead of harder.


---

## Comment 30

> Username: coneiric  
> Created_at: 2018-09-05 17:36:35 UTC  
> Updated_at: 2018-09-19 06:52:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-418816382  

> I would just keep the parser that was there.  
  
Reverted back to a version closer to the original. Cleaned up some spec errors, style issues, and added your buffer class (and friends).  
  
Going to add some more unit-tests to cover the attacks in the papers you linked. Still have the rewritten version in my `loops` branch, if it's preferred.  
  
> Doing a light tidying lets us get something into the tip of the develop and master branches sooner rather than later  
  
Getting something ready for use is my main goal, as well. Hopefully this latest revision is closer to a "light tidying". Much appreciation for your patience and help @vinniefalco.

---

## Comment 31

> Username: vinniefalco  
> Created_at: 2018-09-23 02:54:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-423787973  

Whatever happened with this?

---

## Comment 32

> Username: coneiric  
> Created_at: 2018-09-25 02:07:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-424183321  

> Whatever happened with this?  
  
Hi, just saw your comment. I updated the PR with your recommendations to revert closer to the original parser. Made a few updates, and moved the other rewritten parser to my `loops` branch.  
  
Also, added some unit-tests for cases from the SSRF paper you linked.  
  
Please let me know if there are more changes you would like me to make.

---

## Comment 33

> Username: vinniefalco  
> Created_at: 2018-10-07 18:42:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-427676497  

Okay I finally got some time to look at this. I don't see any of the code from the SOCKS proxy branch? This is just all my code with some added tests? I don't think this is a good idea, my public interfaces are experimental and have problems. For example `static_buffer` and the URI buffer concept in general are not fully thought out and have problems. My routines for categorizing the characters are okay I guess but that's all I would take. Otherwise, we should just base it on the code from the other pull request, which works in the larger context of the SOCKS proxy.

---

## Comment 34

> Username: vinniefalco  
> Created_at: 2018-10-07 18:43:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-427676573  

Ohhhh....I just had a look at parser.ipp, this is from the other branch?

---

## Comment 35

> Username: coneiric  
> Created_at: 2018-10-09 00:11:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-428018109  

> I just had a look at parser.ipp, this is from the other branch?  
  
It is, I am actually using something closer to my `loops` branch in another project. I will update that branch with my most recent changes. The `loops` branch also includes changes for correctness, and a different `buffer` class loosely based on yours.  
  
If you are not opposed to it, I would like to switch out the current parser in this PR with the one in my `loops` branch. It is much easier to make changes there, and (imho) it adheres more closely to RFC3986. This is especially true when compared with the parser from the SOCKS PR.  
  
> my public interfaces are experimental   
  
I loved your use of string view, and was disappointed when I ran into problems getting it to work in another project. Will update the `buffer` class in my `loops` branch with the new interfaces currently in use. Let me know what you think :)  
  
> Otherwise, we should just base it on the code from the other pull request, which works in the larger context of the SOCKS proxy.  
  
The code in my `loops` branch is still loosely based on the code from @Jackarain's PR. The main difference being the split into multiple functions vs. the state machine, and corrections for RFC3986.

---

## Comment 36

> Username: vinniefalco  
> Created_at: 2018-10-09 00:29:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-428020781  

Yeah I'm okay with whatever implementation you think is best for the parser but I have concerns about using my half-baked ideas, even in experimental/

---

## Review 37 [Commented]

> Username: kotbegemot  
> Created_at: 2018-10-30 06:10:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-169623294  

📁 include/boost/beast/experimental/core/uri/parts.hpp

```diff
  41 |+ public:
  42 |+   void scheme(std::string part) { scheme_ = part; }
  43 |+   std::string scheme() const noexcept { return scheme_; }
```

> Username: kotbegemot  
> Created_at: 2018-10-30 06:10:44 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r229185441  

Why use a std::string? Can be use a string_view(C++17/boost.beast).

> Username: coneiric  
> Created_at: 2018-11-06 07:19:16 UTC  
> Updated_at: 2018-11-06 07:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r231017289  

I chose to use std::string because use in Kovri was a bit simpler that way. After a little bit of work, the performance benefits of string_view are worth converting to string when necessary.


---

## Comment 38

> Username: coneiric  
> Created_at: 2018-11-06 07:33:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-436157215  

Pinging @vinniefalco.  
  
I rebased on the latest develop branch, updated the parser with std algorithms, and made some minor changes to the buffer/parts components.

---

## Comment 39

> Username: vinniefalco  
> Created_at: 2018-11-06 23:55:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-436452797  

ack

---

## Comment 40

> Username: coneiric  
> Created_at: 2018-11-08 23:46:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-437198212  

:) What do you think of the most recent set of changes? Is this PR close or ready to be merged?

---

## Comment 41

> Username: vinniefalco  
> Created_at: 2018-11-09 00:31:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-437206945  

I have not looked closely at it but I will do so just as soon as I am done with the preparation for Boost 1.69 which is coming out shortly

---

## Review 42 [Commented]

> Username: Eelis  
> Created_at: 2019-02-27 06:03:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-208347010  

📁 include/boost/beast/experimental/core/uri/detail/parser.hpp

```diff
  24 |+   It find_delimiter_or_mismatch(It first, It last,
  25 |+                                 std::function<bool(char)> delimiter_func,
  26 |+                                 std::function<bool(char)> match_func,
```

> Username: Eelis  
> Created_at: 2019-02-27 06:03:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r260603578  

Using std::function here seems like a premature pessimization. Have you considered taking the callback functor types as template parameters instead?


---

## Review 43 [Commented]

> Username: Eelis  
> Created_at: 2019-02-27 06:09:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1231#pullrequestreview-208348257  

📁 include/boost/beast/experimental/core/uri/rfc3986.hpp

```diff
  31 |+           c == 'E' || c == 'F' || is_digit(c));
  32 |+ }
  33 |+ 
```

> Username: Eelis  
> Created_at: 2019-02-27 06:09:24 UTC  
> Updated_at: 2019-02-27 06:10:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#discussion_r260604637  

Did you intend for the above three functions (and some more further down in the file) to be inline? (or perhaps even constexpr)


---

## Comment 44

> Username: vinniefalco  
> Created_at: 2020-01-23 14:12:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-577698357  

See: https://github.com/vinniefalco/url

---

## Comment 45

> Username: vinniefalco  
> Created_at: 2022-04-05 23:51:10 UTC  
> Url: https://github.com/boostorg/beast/pull/1231#issuecomment-1089538630  

the main repo for Boost.URL is https://github.com/CPPAlliance/url

---
