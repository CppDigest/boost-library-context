# #126 - Implementation details of cpp files should not fo to `include` directory [Closed]

> Username: akrzemi1  
> Created at: 2024-01-19 04:48:32 UTC  
> Updated at: 2024-01-31 07:16:36 UTC  
> Closed at: 2024-01-31 07:16:36 UTC  
> Url: https://github.com/boostorg/charconv/issues/126  

If file https://github.com/cppalliance/charconv/blob/0e9dbf273d215ca717ae98f36e9c15d4f88934c2/include/boost/charconv/detail/compute_float64.hpp#L1[compute_float64.hpp] is only included from the cpp files in `src` directory, I do not think it should be exposed in `include` directory.
