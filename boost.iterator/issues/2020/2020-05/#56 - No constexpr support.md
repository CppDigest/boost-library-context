# #56 - No constexpr support [Open]

> Username: JVApen  
> Created at: 2020-05-22 08:55:09 UTC  
> Updated at: 2020-05-22 08:55:09 UTC  
> Url: https://github.com/boostorg/iterator/issues/56  

With recent versions of C++, it is possible to have constexpr iterators, for example for std::array (and in c++20 for vector and string).  
The boost iterator functionality, like transform_iterator, filter_iterator ... ain't constexpr and as a result can't be used at constexpr time.
