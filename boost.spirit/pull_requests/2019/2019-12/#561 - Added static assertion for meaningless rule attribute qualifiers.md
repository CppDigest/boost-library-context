# #561 Added static assertion for meaningless rule attribute qualifiers [Merged]

> Username: Kojoley  
> Created at: 2019-12-06 15:04:39 UTC  
> Updated at: 2019-12-07 14:12:12 UTC  
> Merged at: 2019-12-07 14:10:42 UTC  
> Closed at: 2019-12-07 14:10:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/561  

```cpp  
qi::rule<char const*, int()>        // ok  
qi::rule<char const*, int const()>  // ok, but strange  
qi::rule<char const*, int&()>       // meaningless reference  
qi::rule<char const*, int const&()> // meaningless reference  
  
ka::rule<char const*, int()>        // ok  
ka::rule<char const*, int const()>  // meaningless const  
ka::rule<char const*, int&()>       // meaningless reference  
ka::rule<char const*, int const&()> // meaningless const and reference  
```

---

## Comment 1

> Username: djowel  
> Created_at: 2019-12-06 15:24:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/561#issuecomment-562615720  

Perhaps I haven't thought this through, but can you explain a bit why we need this?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-12-06 15:37:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/561#issuecomment-562621024  

Because it helps people find misuses easier? They already get an assertion for those misuses but from the `transform_attribute` and they are harder to diagnose.  
  
Btw, I forgot about X3, it also needs this.

---

## Comment 3

> Username: djowel  
> Created_at: 2019-12-07 01:47:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/561#issuecomment-562801927  

> Because it helps people find misuses easier? They already get an assertion for those misuses but from the `transform_attribute` and they are harder to diagnose.  
>   
> Btw, I forgot about X3, it also needs this.  
  
Is there a related ticket or use-case that prompted you to have this in?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-12-07 11:48:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/561#issuecomment-562844302  

https://github.com/mapnik/mapnik/blob/745e393fd694908977d9ab5a38ded3446fed88b3/include/mapnik/json/geometry_generator_grammar.hpp#L89-L115 or https://github.com/mapnik/mapnik/blob/c9da5cbd8e563a6b06b3d72d9a883281d1531962/include/mapnik/json/feature_generator_grammar.hpp where `transform_attribute` specializations were added because of bogus `const&` in the rule, plus I had myself the frustration about constref on attributes personally when just started with Spirit back then.

---

## Comment 5

> Username: djowel  
> Created_at: 2019-12-07 12:26:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/561#issuecomment-562847067  

OK, the reason I am asking is that I think we need to add a test case for this.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2019-12-07 12:36:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/561#issuecomment-562847703  

False-positive tests are just our current test suite. True-positive tests need to trigger compile time error and that is terribly handled with Boost.Build as every check should be a separate test - what hits test suite time significantly, plus compile-fail tests yield error message to the console without ability to toggle them off https://github.com/boostorg/build/issues/262. I can of course put tests and disable them but I do not see any benefits from doing this, also the static assertions are simple enough and I already tested them manually.

---

## Comment 7

> Username: djowel  
> Created_at: 2019-12-07 14:10:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/561#issuecomment-562854440  

Yeah. I think we have a few of those, but I am not sure if they are even tested.

---
