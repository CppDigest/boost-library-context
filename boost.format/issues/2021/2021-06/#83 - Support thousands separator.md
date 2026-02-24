# #83 - Support thousands separator [Open]

> Username: Krzmbrzl  
> Created at: 2021-06-09 07:16:37 UTC  
> Updated at: 2021-06-09 07:24:43 UTC  
> Url: https://github.com/boostorg/format/issues/83  

I know the docs say  
  
> the ' printf option (format with thousands grouping characters)) has no effect in format.  
  
and the code says  
https://github.com/boostorg/format/blob/c1170a6d546b36f9399f3983fad0994e8f946d8f/include/boost/format/parsing.hpp#L172-L173  
  
but it would be really neat if this could be supported in the future.  
  
I assume the issue is that Boost::format is essentially trying to achieve all (or at least the majority)  of its formatting using standard stream flags and there is none for thousands separators?  
  
From https://stackoverflow.com/questions/17530408/print-integer-with-thousands-and-millions-separator it seems that what would be necessary is to use a [imbue](https://en.cppreference.com/w/cpp/io/ios_base/imbue) in combination with a locale.  
Has this been considered yet?  
  
EDIT: This approach seems to at least break when using Boost::multiprecision numbers with the stream at which case the thousands separator is ignored again.
