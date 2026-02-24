# #131 - Error: 'static_mutex' in namespace 'boost' does not name a type [Closed]

> Username: ClaymorePT  
> Created at: 2021-06-03 20:05:21 UTC  
> Updated at: 2021-10-06 12:43:54 UTC  
> Closed at: 2021-10-06 12:43:54 UTC  
> Url: https://github.com/boostorg/regex/issues/131  

It seems that the source `boost/regex/v5/mem_block_cache.hpp` was changed to include `<mutex>` instead of `<boost/regex/pending/static_mutex.hpp>` but the rest of the code still uses `boost::static_mutex` instead of `std::mutex`, along with `BOOST_STATIC_MUTEX_INIT`  
  
```  
gcc.compile.c++ bin.v2/libs/graph/build/gcc-8.4.0/release/threading-multi/visibility-hidden/read_graphviz_new.o  
In file included from ./boost/regex/v5/perl_matcher_non_recursive.hpp:23,  
                 from ./boost/regex/v5/perl_matcher.hpp:572,  
                 from ./boost/regex/v5/regex.hpp:45,  
                 from ./boost/regex.hpp:34,  
                 from libs/graph/src/read_graphviz_new.cpp:46:  
./boost/regex/v5/mem_block_cache.hpp:91:11: error: 'static_mutex' in namespace 'boost' does not name a type  
    boost::static_mutex mut;  
           ^~~~~~~~~~~~  
./boost/regex/v5/mem_block_cache.hpp:91:4: note: suggested alternative: 'static_object'  
    boost::static_mutex mut;  
    ^~~~~  
    static_object  
./boost/regex/v5/mem_block_cache.hpp: In member function 'void* boost::re_detail_500::mem_block_cache::get()':  
./boost/regex/v5/mem_block_cache.hpp:106:37: error: 'mut' was not declared in this scope  
       std::lock_guard<std::mutex> g(mut);  
                                     ^~~  
./boost/regex/v5/mem_block_cache.hpp:106:37: note: suggested alternative: 'put'  
       std::lock_guard<std::mutex> g(mut);  
                                     ^~~  
                                     put  
./boost/regex/v5/mem_block_cache.hpp: In member function 'void boost::re_detail_500::mem_block_cache::put(void*)':  
./boost/regex/v5/mem_block_cache.hpp:120:37: error: 'mut' was not declared in this scope  
       std::lock_guard<std::mutex> g(mut);  
                                     ^~~  
./boost/regex/v5/mem_block_cache.hpp:120:37: note: suggested alternative: 'put'  
       std::lock_guard<std::mutex> g(mut);  
                                     ^~~  
                                     put  
./boost/regex/v5/mem_block_cache.hpp: In static member function 'static boost::re_detail_500::mem_block_cache& boost::re_detail_500::mem_block_cache::instance()':  
./boost/regex/v5/mem_block_cache.hpp:137:52: error: 'BOOST_STATIC_MUTEX_INIT' was not declared in this scope  
       static mem_block_cache block_cache = { 0, 0, BOOST_STATIC_MUTEX_INIT, };  
                                                    ^~~~~~~~~~~~~~~~~~~~~~~  
./boost/regex/v5/mem_block_cache.hpp:137:52: note: suggested alternative: 'BOOST_STATIC_ASSERT'  
       static mem_block_cache block_cache = { 0, 0, BOOST_STATIC_MUTEX_INIT, };  
                                                    ^~~~~~~~~~~~~~~~~~~~~~~  
                                                    BOOST_STATIC_ASSERT  
./boost/regex/v5/mem_block_cache.hpp:137:77: error: too many initializers for 'boost::re_detail_500::mem_block_cache'  
       static mem_block_cache block_cache = { 0, 0, BOOST_STATIC_MUTEX_INIT, };  
                                                                             ^  
  
```

---

## Comment 1

> Username: ClaymorePT  
> Created at: 2021-06-03 22:30:44 UTC  
> Url: https://github.com/boostorg/regex/issues/131#issuecomment-854227853  

I've took the liberty to propose a fix in https://github.com/boostorg/regex/pull/132

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-06 12:43:54 UTC  
> Url: https://github.com/boostorg/regex/issues/131#issuecomment-936165594  

Fix should be in develop now.
