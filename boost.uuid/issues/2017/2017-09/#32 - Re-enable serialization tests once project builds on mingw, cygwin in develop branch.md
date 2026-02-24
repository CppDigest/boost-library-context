# #32 - Re-enable serialization tests once project builds on mingw, cygwin in develop branch [Closed]

> Username: jeking3  
> Created at: 2017-09-10 20:30:48 UTC  
> Updated at: 2017-10-18 19:44:00 UTC  
> Closed at: 2017-10-10 14:14:05 UTC  
> Url: https://github.com/boostorg/uuid/issues/32  

boostorg/serialization won't build clean in all the CI jobs boostorg/uuid has, so I disabled the serialization unit tests which eliminated the need to build serialization as part of the uuid CI builds.  This should be re-enabled once serialization stabilizes.
