# #103 - Use core/lightwieght-test. [Open]

> Username: grafikrobot  
> Created at: 2023-02-08 17:34:29 UTC  
> Updated at: 2023-02-08 17:34:29 UTC  
> Url: https://github.com/boostorg/utility/issues/103  

Currently some of the tests in utility use the BOOST_CHECK macros from the internal type_traits/test directory. There are better equivalents in the Core Lightweight Testing public utility. It would help out to use those core test macros instead to avoid the extra dependency and to likely resolve some of the issues relating to those BOOST_CHECK macros.
