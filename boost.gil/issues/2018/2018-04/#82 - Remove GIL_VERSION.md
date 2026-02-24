# #82 - Remove GIL_VERSION [Closed]

> Username: mloskot  
> Created at: 2018-04-11 22:22:48 UTC  
> Updated at: 2018-06-25 16:11:18 UTC  
> Closed at: 2018-06-25 15:55:40 UTC  
> Url: https://github.com/boostorg/gil/issues/82  

Since 200eecb70261e9ba4c71904f5bd8a13c75a7e6ab we now use `version.hpp`, so this `GIL_VERSION` macro should be removed from https://github.com/boostorg/gil/blob/04bc0ea689ca01bb4793ecacaf34e4f015f034f4/include/boost/gil/gil_config.hpp#L26
