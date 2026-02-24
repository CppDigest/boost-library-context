# #92 - Incorrect base pointer after sync operation [Open]

> Username: atlantisjk  
> Created at: 2019-05-02 17:41:56 UTC  
> Updated at: 2019-05-02 17:41:56 UTC  
> Url: https://github.com/boostorg/iostreams/issues/92  

In the else condition of [indirect_streambuf::sync_impl()](https://github.com/boostorg/iostreams/blob/a31d9de40c957d676a4e9464ab9c6bbc963a9af8/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp#L411), pbase is not correctly set if data has been left in the out buffer during previous sync operations. I suggest to use `setp(pbase() + amt, out().end());` instead.
