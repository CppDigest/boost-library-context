# #504 Unicode charset [Merged]

> Username: cmazakas  
> Created at: 2019-05-04 13:59:37 UTC  
> Updated at: 2019-05-07 22:22:30 UTC  
> Merged at: 2019-05-07 22:22:25 UTC  
> Closed at: 2019-05-07 22:22:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/504  

This pull request gives X3 Unicode literal support for its `char_set<boost::spirit::char_encoding::unicode>`. This passively enables `boost::spirit::x3::unicode::char_` to now support string literals such as: `U"hello, \u20ac!"`.  
  
This PR also extends the X3 `test` utility to work with `boost::basic_string_view` as well.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-05-04 18:06:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#issuecomment-489350620  

Looks good. I'll study this as soon as I fix the (unrelated) failing tests on Visual Studio.

---

## Comment 2

> Username: djowel  
> Created_at: 2019-05-04 19:31:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#issuecomment-489358332  

One comment ^^, otherwise it looks good. I just fixed the (unrelated) VS failing test.

---

## Review 3 [Changes requested]

> Username: djowel  
> Created_at: 2019-05-04 19:32:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/spirit/pull/504#pullrequestreview-233748574  

📁 test/x3/char_set.cpp

```diff
   1 |+ #define BOOST_SPIRIT_X3_UNICODE
```

> Username: djowel  
> Created_at: 2019-05-04 19:28:06 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r280992914  

You'll need a copyright notice at the top with the Boost license. See other files.

> Username: cmazakas  
> Created_at: 2019-05-05 17:46:20 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281031808  

Added!


---

## Review 4 [Commented]

> Username: Kojoley  
> Created_at: 2019-05-05 14:18:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/504#pullrequestreview-233782762  

📁 include/boost/spirit/home/x3/support/traits/string_traits.hpp

```diff
  95 | 
  96 |+     template <>
  97 |+     struct char_type_of<char32_t> : mpl::identity<char32_t> {};
```

> Username: Kojoley  
> Created_at: 2019-05-05 14:18:44 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281024014  

I am inclined towards simplifying and generalizing the `char_type_of` instead because there are more char types and soon or later the question will rise again.

> Username: djowel  
> Created_at: 2019-05-05 14:41:45 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281024918  

> I am inclined towards simplifying and generalizing the `char_type_of` instead because there are more char types and soon or later the question will rise again.  
  
Makes sense. I'd love to see some ideas in code.

> Username: Kojoley  
> Created_at: 2019-05-06 17:29:58 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281281039  

See #507

> Username: cmazakas  
> Created_at: 2019-05-06 17:53:35 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281289637  

Oh cool, that PR obsoletes mine then, no? I'm fine with that. Less code maintenance in general is better.  
  
We can still keep the small tests that I added though as those still have some value.

> Username: djowel  
> Created_at: 2019-05-06 23:43:10 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281405470  

> We can still keep the small tests that I added though as those still have some value.  
  
Absolutely. I just merged #507. Could you please do a final tweak on this PR to deal the changes? Thanks!

> Username: cmazakas  
> Created_at: 2019-05-07 04:02:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281453448  

Alright, took a big of git magic to get there but I managed to condense my PR down to 1 commit that just updates the test files.


---

## Review 5 [Commented]

> Username: Kojoley  
> Created_at: 2019-05-05 14:57:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/504#pullrequestreview-233784479  

📁 test/x3/char_set.cpp

```diff
  20 |+     {
  21 |+         using namespace boost::spirit::x3;
  22 |+         using char_set = char_set<boost::spirit::char_encoding::ascii>;
```

> Username: Kojoley  
> Created_at: 2019-05-05 14:57:28 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281025500  

Ain't we already have those tests?  
  
`char_set` is just an underlying parser of `char_("bla")` if you did not know it https://wandbox.org/permlink/euLu4sbtPOFKnxBg

> Username: djowel  
> Created_at: 2019-05-05 15:01:11 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281025648  

> Ain't we already have those tests?  
  
Good question! I was thinking about that actually. Was it in char1.cpp? Perhaps we can just augment those?

> Username: cmazakas  
> Created_at: 2019-05-05 17:47:52 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281031855  

Ah, Kojoley is correct.  
  
>  … if you did not know it   
  
At the time, I didn't. You are correct, the ASCII tests are repetitious in this case.  
  
Should I simply move the Unicode tests here to the char1.cpp test file?

> Username: djowel  
> Created_at: 2019-05-06 00:01:58 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281044497  

> At the time, I didn't. You are correct, the ASCII tests are repetitious in this case.  
>   
> Should I simply move the Unicode tests here to the char1.cpp test file?  
  
Yes please :-)

> Username: cmazakas  
> Created_at: 2019-05-06 15:55:53 UTC  
> Updated_at: 2019-05-07 03:58:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#discussion_r281246973  

Done!


---

## Comment 6

> Username: cmazakas  
> Created_at: 2019-05-07 15:30:10 UTC  
> Updated_at: 2019-05-07 15:30:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#issuecomment-490129718  

For some reason, the Travis CI link is showing the wrong build job. This is the correct one that passes for the latest changes:  
https://travis-ci.org/boostorg/spirit/builds/529120733

---

## Comment 7

> Username: djowel  
> Created_at: 2019-05-07 22:20:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#issuecomment-490275939  

> For some reason, the Travis CI link is showing the wrong build job. This is the correct one that passes for the latest changes:  
> https://travis-ci.org/boostorg/spirit/builds/529120733  
  
We should be good.

---

## Comment 8

> Username: djowel  
> Created_at: 2019-05-07 22:22:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/504#issuecomment-490276339  

Merged. Many thanks for the splendid PR.

---
