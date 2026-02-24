# #10 Fixes the DEFINE_STRUCT* macros after addition of type deduction for ADAPT_STRUCT [Merged]

> Username: daminetreg  
> Created at: 2014-06-17 20:44:20 UTC  
> Updated at: 2014-06-17 21:54:22 UTC  
> Merged at: 2014-06-17 21:54:22 UTC  
> Closed at: 2014-06-17 21:54:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/10  

Hi @djowel and @K-ballo,  
  
As I told in pull-request #9 the BOOST_FUSION_DEFINE_STRUCT macros were broken by my changes. This small commit fixes this problem. This way all tests are passing now.  
  
As the BOOST_FUSION_ADAPT_STRUCT_FILLER\* macros were changed to handle type deduction, this breaks the usage that BOOST_FUSION_DEFINE_STRUCT makes from them.  
  
That is because BOOST_FUSION_DEFINE_STRUCT expects a simple tuple per attribute, while we now provide a tuple containing the size of a nested tuple which either has or not the type provided ( _i.e._ ((2, (member_type0, member_name0))) ). But in the case of the DEFINE\* macros it would be a non-sense to support this kind of API as a field being currently defined cannot be deduced. :)  
  
Cheers

---
