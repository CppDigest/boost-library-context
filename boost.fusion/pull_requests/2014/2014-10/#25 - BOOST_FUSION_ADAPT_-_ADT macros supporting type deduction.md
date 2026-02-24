# #25 BOOST_FUSION_ADAPT_*_ADT macros supporting type deduction [Merged]

> Username: daminetreg  
> Created at: 2014-10-22 20:48:47 UTC  
> Updated at: 2014-10-27 10:52:53 UTC  
> Merged at: 2014-10-23 00:48:07 UTC  
> Closed at: 2014-10-23 00:48:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/25  

Hi @djowel and @K-ballo,  
  
I'm happy to come back to you with the last changes to bring type deduction for the BOOST_FUSION_ADAPT_*_ADT macros. :laughing:   
It took quite more time, as I expected but it's better late than never.  
  
These changes contains support to deduce the types of the fields listed in :   
- BOOST_FUSION_ADAPT_ADT  
- BOOST_FUSION_ADAPT_TPL_ADT  
- BOOST_FUSION_ADAPT_ASSOC_ADT  
- BOOST_FUSION_ADAPT_ASSOC_TPL_ADT  
  
This fully completes the work for the patch I proposed in december 2013 : _DRYing BOOST_FUSION_ADAPT_STRUCT with decltype/Boost.TypeOf_. (Actually I'm thirsty so I don't like this pun, sadly it's difficult to take a :beer: over internet, but may you come to Europe I would be happy to invite you IRL :wink:).  
  
So this pull-request may close https://svn.boost.org/trac/boost/ticket/9516.  
## Tests  
  
The changes passes all tests on g++-4.6, g++-4.8, g++-4.8-c++11, run with or without BOOST_PP_VARIADICS support. I'll run in the coming days the tests on msvc, clang and mingw to ensure that it all work as expected, I'm not expecting much issues, but who knows with things like emptiness detection of variadic macro arguments... It will certainly results in an additional pull-request for compile issue fixes.  
## Review of these changes  
  
For the review of the changes that I would be happy to get from you, I need to make you aware of _one open question about  attribute_const_type and attribute_type_ :  
I've blocked long this point, but as I looked at it from all angles I came to the decision that when deducing types attribute_const_type is always const-qualified and attribute_type is always non-const. This  can be seen in the last commit I did : https://github.com/daminetreg/fusion/commit/e50f5852e4cb7816de90f5e1dd15966a9cb659c5.   
  
Do you agree with this, or am I totally wrong about the purpose of attribute_const_type and attribute_type ?  
  
Cheers,  
I stay to disposal for any further changes, a full-rewrite or a :beer:

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-23 00:47:33 UTC  
> Url: https://github.com/boostorg/fusion/pull/25#issuecomment-60178235  

@K-ballo, @daminetreg, I'll be merging this PR so we can play around with it and see the actual code in action. It's in a branch anyway, so there is no risk. Let's make it a goal to merge this back to develop soon. Nice work, Damien!

---

## Comment 2

> Username: daminetreg  
> Created_at: 2014-10-27 09:54:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/25#issuecomment-60569771  

Thanks :)  
Would it help if I merge develop in my branch and ensure everything runs, tests and so on ?

---

## Comment 3

> Username: djowel  
> Created_at: 2014-10-27 09:58:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/25#issuecomment-60570176  

Yes, please! That would be great!

---

## Comment 4

> Username: daminetreg  
> Created_at: 2014-10-27 10:52:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/25#issuecomment-60575781  

I'll pull-request the state with develop merged tomorrow evening then.

---
