# #28 Add array constructors for string_view [Closed]

> Username: LRFLEW  
> Created at: 2016-12-31 23:19:13 UTC  
> Updated at: 2017-01-04 06:41:17 UTC  
> Closed at: 2017-01-04 06:41:17 UTC  
> Url: https://github.com/boostorg/utility/pull/28  

This additional constructor is intended to make constructing a constant expression string_view easier in C++11 and C++14. Consider the following code snippet: `constexpr boost::string_view my_string("Hello World!");` This won't compile in C++11/14 because this constructor will try to call `std::char_traits<char>::length()`, which wasn't made constexpr until the (upcoming) C++17 standard. This can be worked around by changing the line to `constexpr boost::string_view my_string("Hello World!", 12);`, but that is more error-prone, and makes it harder to update it later.  
  
This new constructor makes use of the fact that the type of a string literal is `const char[N]` where `N` is the length of the string plus one (for the null character). With this change, when you compile the line `constexpr boost::string_view my_string("Hello World!");`, it sets the length to `N-1`, which is a constant expression. I also added a constructor that additionally takes a bool argument which, when true, sets the string length to `N` instead of `N-1`. This is for the use case where it's an actual character array (such as `{'H', 'e', 'l', 'l', 'o'}`) without a null terminator.  
  
This constructor should be valid for all use cases, but it may pose a problem when the user defines a custom character traits class that overrides the `length()` function. If you think that may be a problem, then I can change this constructor to be in a partial template specification so it can only be used when the traits class is `std::char_traits<charT>`, where this trick is guaranteed to be valid.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-01-01 17:06:42 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-269910737  

The Library Evolution group of the C++ standard committee burned probably 10 hours of committee time discussing this exact issue.  (Multiple times, in fact) The decision there was that constructing a `string_view` from an array would give unexpected results to some subset of users - either the ones that were constructing from a string literal, or the ones that were constructing from an actual array.  
  
Also discussed was the (rare) case where the string literal contains an embedded null - and so the constructor that calls `traits::length` gives a different answer than the one that you're adding.  
  
[ Just pointing out that there's a lot of backstory here ]

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-01-01 18:04:15 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-269912905  

Just to add a few alternatives to consideration:  
  
- There is a `string_literal` class in Boost.Log (https://github.com/boostorg/log/blob/develop/include/boost/log/utility/string_literal.hpp). While its intent is different from just optimizing construction from a literal, it does have a `constexpr` initializing constructor. Maybe it would make sense to extract that class into Boost.Utility and add an easy way to obtain a `string_view` from it.  
- We could add a factory function that would create a `string_view` from a literal by automatically deducing its length.

---

## Comment 3

> Username: LRFLEW  
> Created_at: 2017-01-02 21:09:41 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-270020549  

@mclow Yeah, I had a feeling that if it made it into the C++17 standard that all the details about it had been talked to extreme detail. I was a little confused by the explanations you had, but I think I understand. At first, I was confused as to why having the length differ from the one returned by `traits::length()` was an issue because the constructor that takes in a length as a second argument would do that already. However, I can see how it can cause confusion. It's looks ambiguous which constructor would be used for `string_view("Hello\0 World!");` (either the one taking a pointer, or the one taking an array), and the two constructors would produce two different results.  
  
Of @Lastique's suggestions, I think the factory function would be the most practical. I ended up implementing the factory function in my own project to work around this issue. However, it would be more convenient to have it as a constructor (for implicit casting purposes).   
  
I do think there is a better solution, though. The behavior for `std::char_traits<charT>::length()` is well defined, so this could be resolved using partial template specification. I'll go ahead and force-push that solution, as I think it's the better choice at this point.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-01-02 21:21:24 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-270021511  

People wanted to avoid this:  
  
     const char s1[] = "Hello";  
     const char *s2 = "Hello";  
     string_view sv1{s1};  
     string_view sv2{s2};  
     assert(sv1 == sv2);  /// this would fail, because sv1.size() == 6 while sv2.size() == 5

---

## Comment 5

> Username: LRFLEW  
> Created_at: 2017-01-03 06:39:21 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-270058590  

Looking at the code again, I'm starting to think this is a bad idea. The partial-specialization *could* work, but it would require reworking how the code is laid out to make it work (probably requiring a parent class for both the generic and specialized versions to inherit from to limit redundancy). It might make more sense for me to just make my own char_traits class and just use that at this point.  
  
I've also realized that, for my project, this won't work quite right, as I need the `operator==` to be C++11 constexpr as well (I think right now it is essentially C++14 constexpr, but is missing the `BOOST_CXX14_CONSTEXPR` tag; I will try to PR that fix). I'll probably copy out this class and adapt it for my project.  
  
If there are no objections, I'll just close this PR.

---

## Comment 6

> Username: mclow  
> Created_at: 2017-01-03 06:41:59 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-270058762  

Writing your own traits class where `length()` is constexpr is pretty easy.  I have one laying around somewhere; I can send you a copy if you want.

---

## Comment 7

> Username: LRFLEW  
> Created_at: 2017-01-03 06:45:43 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-270059035  

@mclow I already wrote one for my attempt at the partial template specialization, but never pushed it because it was broken. Thanks for the offer, though.

---

## Comment 8

> Username: mclow  
> Created_at: 2017-01-03 06:49:39 UTC  
> Url: https://github.com/boostorg/utility/pull/28#issuecomment-270059278  

I should really dig mine up, write a bunch of tests, and then add it to the Boost.StringAlgo library

---
