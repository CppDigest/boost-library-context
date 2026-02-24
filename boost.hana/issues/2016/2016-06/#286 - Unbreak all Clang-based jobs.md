# #286 - [Travis] Unbreak all Clang-based jobs [Closed]

> Username: ldionne  
> Created at: 2016-06-11 19:51:34 UTC  
> Updated at: 2016-06-12 00:09:18 UTC  
> Closed at: 2016-06-12 00:09:18 UTC  
> Url: https://github.com/boostorg/hana/issues/286  

Since LLVM has disabled its apt mirrors, all of our Travis jobs that require the mirrors are broken. To workaround this, we could install Clang manually using prebuilt binaries.
