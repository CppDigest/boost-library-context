# #242 Allow per-element comparison of string against non-string iterable [Open]

> Username: ecatmur  
> Created at: 2019-12-16 10:53:55 UTC  
> Updated at: 2024-05-31 08:43:29 UTC  
> Url: https://github.com/boostorg/test/pull/242  

(of char).  
  
Test.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2020-04-08 14:09:50 UTC  
> Updated_at: 2020-04-08 14:10:25 UTC  
> Url: https://github.com/boostorg/test/pull/242#issuecomment-610982418  

This is being superseded by the branch `topic/PR-187-BOOST_TEST-with-tolerance-message`. It would be nice if you can give a try to see if this addresses your needs.  
  
@ecatmur

---

## Comment 2

> Username: ecatmur  
> Created_at: 2020-04-08 16:43:47 UTC  
> Updated_at: 2020-04-08 16:44:48 UTC  
> Url: https://github.com/boostorg/test/pull/242#issuecomment-611066673  

@raffienficiaud thanks, I've tested and I still need the change for comparisons to work. I've rebased my patch and force pushed.  
  
My commit is the last one, https://github.com/boostorg/test/pull/242/commits/50826175f8fd19e94fb85ac4db3de31fed3f74a0

---

## Review 3 [Commented]

> Username: raffienficiaud  
> Created_at: 2020-04-08 17:10:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/242#pullrequestreview-390169804  

📁 include/boost/test/tools/collection_comparison_op.hpp

```diff
 397 |-     &&   !unit_test::is_cstring_comparable<Rhs>::value>::type> {    \
 396 |+     && !(unit_test::is_cstring_comparable<Lhs>::value               \
 397 |+          && unit_test::is_cstring_comparable<Rhs>::value)>::type> { \
```

> Username: raffienficiaud  
> Created_at: 2020-04-08 17:10:18 UTC  
> Updated_at: 2020-04-12 00:57:07 UTC  
> Url: https://github.com/boostorg/test/pull/242#discussion_r405681679  

This will likely break other stuff: the collection comparison is mutually exclusive with the cstring comparison operator definition in `include/boost/test/tools/cstring_comparison_op.hpp`. The latter calls the collection comparison, but it should be the cstring comparison that captures the expression.   
  
If the condition changes in this collection comparison (`include/boost/test/tools/collection_comparison_op.hpp`), the same changes should be reflected in the cstring comparison set of operators.   
  
The only way I see is   
  
1. change in `include/boost/test/tools/cstring_comparison_op.hpp`: making the `enable_if` with an `||` condition:  
```  
#define DEFINE_CSTRING_COMPARISON( oper, name, rev, name_inverse )  \  
template<typename Lhs,typename Rhs>  
struct name<Lhs,Rhs,typename boost::enable_if_c<  
    (   unit_test::is_cstring_comparable<Lhs>::value  
     || unit_test::is_cstring_comparable<Rhs>::value)  
    >::type >  
```  
  
2. change in `include/boost/test/tools/collection_comparison_op.hpp` to have `!(unit_test::is_cstring_comparable<Lhs>::value || unit_test::is_cstring_comparable<Rhs>::value)>::type`

> Username: ecatmur  
> Created_at: 2020-04-11 16:22:14 UTC  
> Updated_at: 2020-04-12 00:57:07 UTC  
> Url: https://github.com/boostorg/test/pull/242#discussion_r407082167  

Well, both sides need to be cstring comparable for cstring comparison to apply, so the operator should be `&&` - which is what I'm proposing.  
  
Currently, if both sides are cstring comparable then the cstring comparison is selected; if neither are cstring comparable the collection comparison is selected; if only one side is cstring comparable then neither is selected and compilation fails.  
  
I'm proposing to fill in the gap so that if only one side is cstring comparable it falls back to collection comparison. If it'd be clearer, I could de Morgan's rewrite the collection comparison condition to have `(!unit_test::is_cstring_comparable<Lhs>::value || !unit_test::is_cstring_comparable<Rhs>::value)`?

> Username: raffienficiaud  
> Created_at: 2020-04-11 17:08:37 UTC  
> Updated_at: 2020-04-12 00:57:07 UTC  
> Url: https://github.com/boostorg/test/pull/242#discussion_r407086966  

If any side is `cstring` it should be captured by the `cstring` operation, the other side should be "something" that has the same forward semantics as the `cstring`, and that can be transformed via the `deduce_cstring_transform`.  
  
Currently the string comparison operator is forwarding to the collection comparison, but some string specific operations may happen there (such as encoding-aware comparison) prior to sending to the collection comparison.  
  
There should be a switch that uses `deduce_cstring_transform` for `cstring` or a no-op otherwise, for the `rhs_char_type` part. It should however be restricted enough to avoid capturing:  
  
```  
BOOST_TEST( "abc" == std::vector<int>{'a', 'b', 'c'}).  
```  
  
For that, I guess an extra condition to the `unit_test::is_forward_iterable<Lhs>::value` should be that `typename Rhs::value_type` is `char` or `wchar_t` (or using `is_cstring_impl` via a pointer to `value_type`, also `Rhs` can be an array ...).

> Username: raffienficiaud  
> Created_at: 2020-04-11 17:27:43 UTC  
> Updated_at: 2020-04-12 00:57:07 UTC  
> Url: https://github.com/boostorg/test/pull/242#discussion_r407088952  

One more thing, having the `per_element` modifier should branch to   
  
```  
typename boost::enable_if_c<  
    (unit_test::is_cstring<Lhs>::value || unit_test::is_cstring<Rhs>::value),  
    assertion_result>::type  
element_compare( Lhs const& lhs, Rhs const& rhs )  
```  
  
Isn't it working like that already?

> Username: ecatmur  
> Created_at: 2020-04-11 20:38:20 UTC  
> Updated_at: 2020-04-12 00:57:07 UTC  
> Url: https://github.com/boostorg/test/pull/242#discussion_r407108387  

No, currently it (comparing e.g. `std::string` to `std::vector<char>` with per_element) isn't picked up by either DEFINE_COLLECTION_COMPARISON or DEFINE_CSTRING_COMPARISON so it falls through to DEFINE_CONST_OPER.  
  
I'll have a look what it would take for DEFINE_CSTRING_COMPARISON to support non-string iterables on one side.

> Username: ecatmur  
> Created_at: 2020-04-12 00:17:23 UTC  
> Updated_at: 2020-04-12 00:57:07 UTC  
> Url: https://github.com/boostorg/test/pull/242#discussion_r407126137  

I don't think there's any need for `deduce_cstring_transform` to transform non-string iterables; does it really make sense to treat e.g. `std::list<char>` as a string? As a user I'd be happy to use `per_element()`, or if I really intended to compare as a string then `boost::copy_range<std::string>`.  
  
Leaving the forward iterable side as-is makes for a simpler implementation (and diff - see latest force push). Thanks!

> Username: ecatmur  
> Created_at: 2020-04-12 00:59:39 UTC  
> Url: https://github.com/boostorg/test/pull/242#discussion_r407129210  

note - the reason to check that the other side is forward iterable is that this is required to access `value_type`:  
```  
    typedef name<                                                   \  
        typename lhs_char_type::value_type,                         \  
        typename rhs_char_type::value_type> elem_op;                \  
 ```  
Without this restriction, the `class_properties` tests fail.


---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2020-04-08 17:11:03 UTC  
> Url: https://github.com/boostorg/test/pull/242#issuecomment-611080383  

@ecatmur thank you for the quick feedback. I commented on the PR, I can give a spin on my CI, travis takes ages.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2023-06-27 18:32:44 UTC  
> Url: https://github.com/boostorg/test/pull/242#issuecomment-1610025851  

## [Codecov](https://app.codecov.io/gh/boostorg/test/pull/242?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@7371c5c`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit).  
> Patch has no changes to coverable lines.  
  
> :exclamation: Current head 2648a1b differs from pull request most recent head 2d4d5c3. Consider uploading reports for the commit 2d4d5c3 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@            Coverage Diff             @@  
##             develop     #242   +/-   ##  
==========================================  
  Coverage           ?   55.77%             
==========================================  
  Files              ?      112             
  Lines              ?     5897             
  Branches           ?     2382             
==========================================  
  Hits               ?     3289             
  Misses             ?      778             
  Partials           ?     1830             
```  
  
  
  
  
</details>  
  
[:umbrella: View full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/test/pull/242?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).     
:loudspeaker: Do you have feedback about the report comment? [Let us know in this issue](https://about.codecov.io/codecov-pr-comment-feedback/?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 6

> Username: 13steinj  
> Created_at: 2024-04-19 05:17:32 UTC  
> Url: https://github.com/boostorg/test/pull/242#issuecomment-2065779508  

Chiming in here-- I'm sad to report that @ecatmur has passed away over the new-years holiday period. [Commemoration / memorial / donate to good cause page in case interested](https://edwardjamescatmur.muchloved.com/).  
  
This patch while in place at my organization, seemingly breaks compilation of some tests in Boost.MySQL as in Boost 1.85.0. Presumably due to some dependence on the new Boost.Charconv.  
  
To be perfectly honest with you, I didn't bother figuring out the compile error, I tested against our tests internally and the heavy hitters where I would have expected a problem all compiled and passed / expected failed in the right places; so internally we're just going to undo the patch and if someone was relying on it tell them to convert their container to a string or similar course of action; it's not as if such unit tests are performance critical and can't afford the copy or anything.  
  
Felt necessary to comment in case this would have gotten merged unknowingly.

---
