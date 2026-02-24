# #499 - msvc::no_unique_address [Open]

> Username: mglisse  
> Created at: 2024-06-22 10:40:41 UTC  
> Updated at: 2024-06-22 10:40:41 UTC  
> Url: https://github.com/boostorg/config/issues/499  

According to https://devblogs.microsoft.com/cppblog/msvc-cpp20-and-the-std-cpp20-switch/#c20-no_unique_address , MSVC ignores `[[no_unique_address]]`, but some versions provide `[[msvc::no_unique_address]]` and actually act on it (this has also been added to compatible compilers like clang-18+).  
Would it make sense for BOOST_ATTRIBUTE_NO_UNIQUE_ADDRESS to expand to this attribute under the right conditions?
