# #10 Vc12 does not support default move constructors [Merged]

> Username: jhunold  
> Created at: 2014-05-13 14:01:40 UTC  
> Updated at: 2014-06-15 20:51:08 UTC  
> Merged at: 2014-05-14 23:07:40 UTC  
> Closed at: 2014-05-14 23:07:40 UTC  
> Url: https://github.com/boostorg/graph/pull/10  

Current regression tests for vc12 are all red due to missing compiler support for defaulted move constructors. See http://msdn.microsoft.com/en-us/library/dn457344.aspx and http://blogs.msdn.com/b/vcblog/archive/2013/06/28/c-11-14-stl-features-fixes-and-breaking-changes-in-vs-2013.aspx for the overview. And http://msdn.microsoft.com/en-us/library/hh567368.aspx states:  
"Defaulted and deleted functions  
These are now supported, but with this exception: For defaulted functions, the use of =default to request member-wise move constructors and move assignment operators is not supported. The copies and moves don't interact precisely like the Standard says they should—for example, deletion of moves is specified to also suppress copies, but Visual C++ in Visual Studio 2013 does not."  
So this is a first at shot at working around this limitation. It might by better to add another BOOST_NO_CXX11_DEFAULT_MOVE as at least qcc- 4.4.2_x86 is failing with the same symptoms. And I had to hack copy constructor and operator= and not all test are working.

---
