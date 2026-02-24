# #565 pct-encoding overloads [Closed]

> Username: alandefreitas  
> Created at: 2022-09-20 02:12:47 UTC  
> Updated at: 2022-10-13 04:47:19 UTC  
> Closed at: 2022-10-11 00:09:26 UTC  
> Url: https://github.com/boostorg/url/pull/565  

fix #417

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-20 02:16:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1113060610  

📁 include/boost/url/decode.hpp

```diff
  84 |-     CharSet const& allowed = {}) noexcept;
  73 |+     BOOST_URL_PCT_STRING_VIEW s,
  74 |+     BOOST_URL_STRTOK_ARG(token),
```

> Username: vinniefalco  
> Created_at: 2022-09-20 02:15:59 UTC  
> Updated_at: 2022-09-20 02:16:00 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r974825002  

Are you sure the token should come before the opts?

> Username: alandefreitas  
> Created_at: 2022-09-24 04:11:30 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r979150712  

Not at all :)  
  
Done.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-20 02:16:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1113060752  

📁 include/boost/url/detail/encode.hpp

```diff
 422 |+ template<
 423 |+     class CharSet =
 424 |+         grammar::all_chars_t>
```

> Username: vinniefalco  
> Created_at: 2022-09-20 02:16:18 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r974825100  

the implementation signature should not have a defaulted template argument

> Username: alandefreitas  
> Created_at: 2022-09-24 04:11:48 UTC  
> Updated_at: 2022-09-24 04:11:49 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r979150757  

OK. I left the template argument in `url/encode.hpp` only


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-20 02:17:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1113061162  

📁 include/boost/url/encode.hpp

```diff
 149 |- encode(
 150 |-     char* dest,
 151 |-     char const* end,
```

> Username: vinniefalco  
> Created_at: 2022-09-20 02:17:10 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r974825404  

I don't think we can take this function away, because there is no way for the user to get the same behavior using a string token

> Username: alandefreitas  
> Created_at: 2022-09-24 04:11:57 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r979150771  

I returned with the function to `url/encode.hpp`. Shouldn't `url/decode.hpp` have an analogous function then?

> Username: vinniefalco  
> Created_at: 2022-10-03 22:36:21 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986291004  

I'm not sure what that means

> Username: alandefreitas  
> Created_at: 2022-10-04 08:37:03 UTC  
> Updated_at: 2022-10-04 08:37:04 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986589980  

It means "should we have two overloads of decode, like we have two overload of encode"?  
  
Something that takes `char*`:  
  
```cpp  
template<  
    class CharSet = grammar::all_chars_t>  
std::size_t  
encode(  
    char* dest,  
    char const* end,  
    string_view s,  
    decode_opts const& opt = {});  
```  
  
The rationale would be the same as for encode.hpp: "I don't think we can take this function away, because there is no way for the user to get the same behavior using a string token"

> Username: vinniefalco  
> Created_at: 2022-10-04 14:08:13 UTC  
> Updated_at: 2022-10-04 14:08:14 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986915921  

very likely yes


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-20 02:18:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1113061665  

📁 include/boost/url/encode.hpp

```diff
 220 |-     CharSet const& allowed = {},
 221 |-     Allocator const& a = {});
 152 |+     CharSet const& allowed = {}) noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-09-20 02:18:11 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r974825779  

umm... does encoding with `all_chars` as the allowed set make any sense? I have my doubts that these parameters are in the right order. I would think the charset comes first, since you cannot encode with out. Then the opts, and last the string token. We should discuss

> Username: alandefreitas  
> Created_at: 2022-09-24 04:12:06 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r979150783  

> does encoding with all_chars as the allowed set make any sense?  
  
Regarding the `allowed` set, I think:  
  
- the functionality is reasonable because only replacing all `%`s is something too simple to achieve without this function but  
- the way we reached this parameter name led to something confusing because all characters are technically "allowed" in that encoding context. What we are really defining here is what chars are going to be encoded.  
  
> I have my doubts that these parameters are in the right order. I would think the charset comes first, since you cannot encode with out. Then the opts, and last the string token. We should discuss  
  
I've moved the token as the last parameter.   
  
Regarding the order of `opt` and `allowed`, I have no opinion. Considering that the `url` setters will reencode and especially when the default for `space_to_plus` is false, none of them are really mandatory.  
  
In the meanwhile, I moved the charset before `opt` anyway.

> Username: vinniefalco  
> Created_at: 2022-10-03 22:33:41 UTC  
> Updated_at: 2022-10-03 22:33:42 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986289661  

> the functionality is reasonable because only replacing all %s is something too simple to achieve without this function but  
  
That's my point: a user never wants to "only encode percent characters" - this never comes up. Or do you have a motivating use-case?  
  
> the way we reached this parameter name led to something confusing because all characters are technically "allowed" in that encoding context. What we are really defining here is what chars are going to be encoded.  
  
"allowed" is short for "allowed unescaped".  
  
We could rename it to "unreserved?" or "unescaped?" What do you think?

> Username: alandefreitas  
> Created_at: 2022-10-04 08:39:41 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986592699  

> Or do you have a motivating use-case?  
  
So the proposal is to not have a default parameter?  
  
> We could rename it to "unreserved?" or "unescaped?" What do you think?  
  
I think `unescaped` is the most descriptive

> Username: vinniefalco  
> Created_at: 2022-10-04 14:16:06 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986926290  

Yes the proposal is not to have a default. If you like unescaped we can change it.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-20 02:19:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1113062458  

📁 include/boost/url/impl/decode.hpp

```diff
  30 |+     StringToken&& token,
  31 |+     decode_opts const& opt) noexcept ->
  32 |+         result< BOOST_URL_STRTOK_RETURN >
```

> Username: vinniefalco  
> Created_at: 2022-09-20 02:19:43 UTC  
> Updated_at: 2022-09-20 02:19:44 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r974826373  

hmm I don't think you can just put `BOOST_URL_STRTOK_RETURN` as a template param in `result`, but also this is not emitted in the docs since it is in impl/ so we don't need any of these macros

> Username: alandefreitas  
> Created_at: 2022-09-24 04:12:16 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r979150789  

![image](https://user-images.githubusercontent.com/5369819/192077904-725e54fb-df83-46fc-8cb3-47daa2d4b985.png)

> Username: vinniefalco  
> Created_at: 2022-10-03 19:44:19 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986158265  

I don't like this at all, returning `result<StringToken::result_type>`, this is weird

> Username: alandefreitas  
> Created_at: 2022-10-03 21:25:21 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986241763  

What do you suggest?

> Username: vinniefalco  
> Created_at: 2022-10-03 22:35:34 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986290646  

Well, look at the function  
  
```  
template< class StringToken >  
auto  
decode(  
    pct_string_view s,  
    StringToken&& token,  
    decode_opts const& opt) noexcept ->  
        result< typename StringToken::result_type >  
{  
    return s.decode( opt, std::move(token) );  
}  
```  
  
How can this possibly fail? `pct_string_view` is already validated, so failure is impossible. Also, `s.decode()` doesn't return `result<>` so I'm not sure what is going on here? Lets discuss...

> Username: alandefreitas  
> Created_at: 2022-10-04 08:40:59 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986594012  

Oh... OK. I got a little confused by the pct_string_view being documented as string_view.


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-09-20 02:27:44 UTC  
> Updated_at: 2022-10-09 21:06:02 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1251760567  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/565?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#565](https://codecov.io/gh/boostorg/url/pull/565?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (357c856) into [develop](https://codecov.io/gh/boostorg/url/commit/67b444405736c1157d9c7a23bbd7470e3026a538?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (67b4444) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 357c856 differs from pull request most recent head f48617d. Consider uploading reports for the commit f48617d to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/565/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/565?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #565      +/-   ##  
===========================================  
+ Coverage    96.81%   96.82%   +0.01%       
===========================================  
  Files          139      139                
  Lines         6680     6677       -3       
===========================================  
- Hits          6467     6465       -2       
+ Misses         213      212       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/565?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/url/detail/impl/any\_segments\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3NlZ21lbnRzX2l0ZXIuaXBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/encode.hpp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2VuY29kZS5ocHA=) | `81.81% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/any\_params\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3BhcmFtc19pdGVyLmlwcA==) | `99.41% <100.00%> (ø)` | |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `79.20% <100.00%> (+0.33%)` | :arrow_up: |  
| [include/boost/url/impl/decode.hpp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9kZWNvZGUuaHBw) | `100.00% <100.00%> (+20.00%)` | :arrow_up: |  
| [include/boost/url/impl/encode.hpp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lbmNvZGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.02% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `98.60% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/url/decode\_view.hpp](https://codecov.io/gh/boostorg/url/pull/565/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/565?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/565?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [67b4444...f48617d](https://codecov.io/gh/boostorg/url/pull/565?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-09-20 15:12:52 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1252507202  

LCOV code coverage report [https://565.url.prtest.cppalliance.org/genhtml/index.html](https://565.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-03 22:30:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1129105372  

📁 include/boost/url/detail/impl/encode.hpp

```diff
  33 |+     CharSet const& allowed)
  34 |+ {
  35 |+     /*  If you get a compile error here, it
```

> Username: vinniefalco  
> Created_at: 2022-10-03 22:30:14 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r986287941  

little spacing/formatting glitch here, is your IDE getting in the way?


---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-10-04 08:53:08 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1266618354  

GCOVR code coverage report [https://565.url.prtest.cppalliance.org/gcovr/index.html](https://565.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://565.url.prtest.cppalliance.org/genhtml/index.html](https://565.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-10-04 18:33:24 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1267417417  

GCOVR code coverage report [https://565.url.prtest.cppalliance.org/gcovr/index.html](https://565.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://565.url.prtest.cppalliance.org/genhtml/index.html](https://565.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-10-07 20:58:05 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1272081856  

GCOVR code coverage report [https://565.url.prtest.cppalliance.org/gcovr/index.html](https://565.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://565.url.prtest.cppalliance.org/genhtml/index.html](https://565.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-10-07 21:33:14 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1272103611  

GCOVR code coverage report [https://565.url.prtest.cppalliance.org/gcovr/index.html](https://565.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://565.url.prtest.cppalliance.org/genhtml/index.html](https://565.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 13

> Username: alandefreitas  
> Created_at: 2022-10-07 21:59:44 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1272117148  

The decode overload we came up with still has a problem:  
  
```  
std::size_t  
decode(  
    char* dest,  
    char const* end,  
    BOOST_URL_PCT_STRING_VIEW s,  
    decode_opts const& opt = {});  
```  
  
doesn't return `result<...>` because `pct_string_view` is always valid, but `detail::decode` can still return an error if the destination is too small.

---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-08 00:13:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1135150117  

📁 include/boost/url/encode.hpp

```diff
 128 |-     @param allowed The set of characters
 129 |-     allowed to appear unescaped.
 130 |+     @param unescaped The set of characters
```

> Username: vinniefalco  
> Created_at: 2022-10-08 00:13:01 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990558810  

If you are going to rename it then we have to rename it everywhere. `encoded_size_impl` above still says "allowed."


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-08 00:13:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1135150273  

📁 include/boost/url/encode.hpp

```diff
 129 |-     allowed to appear unescaped.
 130 |+     @param unescaped The set of characters
 131 |+     unescaped to appear unescaped.
```

> Username: vinniefalco  
> Created_at: 2022-10-08 00:13:44 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990558936  

"unescaped The set of characters unescaped to appear unescaped?"


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-08 00:16:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1135150798  

📁 include/boost/url/decode.hpp

```diff
  26 | 
  26 |- /** Validate and decode a string view
  27 |+ /** Decode a string view into a buffer.
```

> Username: vinniefalco  
> Created_at: 2022-10-08 00:16:26 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990559410  

We shouldn't say "decode a string view" (the "string view" part is what I am objecting to here). We are decoding a string. And it doesn't matter that it is "into a buffer", that's implied by the parameter list. The brief can simply say "Apply percent-decoding to a string"


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-08 00:16:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1135150849  

📁 include/boost/url/decode.hpp

```diff
  38 |+     @par Example
  39 |+     @code
  40 |+     char *dest = new char[MAX_LENGTH];
```

> Username: vinniefalco  
> Created_at: 2022-10-08 00:16:46 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990559450  

MAX_LENGTH can't compile? is this javadoc tested?

> Username: alandefreitas  
> Created_at: 2022-10-09 20:48:49 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990838098  

https://github.com/alandefreitas/url/blob/8e7e7f0da3b9a622a432a6d7bb450c57c1c5572f/test/unit/decode.cpp#L433-L437

> Username: vinniefalco  
> Created_at: 2022-10-09 20:54:31 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990838744  

MAX_LENGTH is not in the code snippet


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-08 00:17:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1135150885  

📁 include/boost/url/decode.hpp

```diff
  49 |+     Throws nothing.
  50 |+ 
  51 |+     @return `true` if the output was large
```

> Username: vinniefalco  
> Created_at: 2022-10-08 00:17:00 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990559492  

but it returns a size not a bool


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-08 00:19:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/565#pullrequestreview-1135151247  

📁 include/boost/url/decode.hpp

```diff
  95 |     @par Example
  96 |     @code
  97 |     result< decode_view > r = decode( "Program%20Files" );
```

> Username: vinniefalco  
> Created_at: 2022-10-08 00:19:01 UTC  
> Url: https://github.com/boostorg/url/pull/565#discussion_r990559773  

The example totally doesn't match the function


---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-10-09 20:57:28 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1272626522  

GCOVR code coverage report [https://565.url.prtest.cppalliance.org/gcovr/index.html](https://565.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://565.url.prtest.cppalliance.org/genhtml/index.html](https://565.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-10-09 21:10:42 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1272628842  

GCOVR code coverage report [https://565.url.prtest.cppalliance.org/gcovr/index.html](https://565.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://565.url.prtest.cppalliance.org/genhtml/index.html](https://565.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 22

> Username: vinniefalco  
> Created_at: 2022-10-09 21:12:54 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1272629163  

I'm working on fixing this branch please don't make changes

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2022-10-11 00:09:26 UTC  
> Url: https://github.com/boostorg/url/pull/565#issuecomment-1273929943  

patched and merged

---
