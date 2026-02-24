# #256 - boost 1.61.0 uuid random_generator doesn't compile with -Werror=pedantic [Closed]

> Username: ssmlp001  
> Created at: 2019-03-01 21:55:11 UTC  
> Updated at: 2019-03-01 21:58:58 UTC  
> Closed at: 2019-03-01 21:58:58 UTC  
> Url: https://github.com/boostorg/boost/issues/256  

centos 4.7 and g++ 4.8.5  
g++ -std=c++11 -Werror=pedantic  
```  
boost-1.61.0/include/boost/uuid/seed_rng.hpp: In member function 'void boost::uuids::detail::seed_rng::sha1_random_digest_()':  
/export/home/software/boost-1.61.0/include/boost/uuid/seed_rng.hpp:222:60: error: ISO C++ forbids casting between pointer-to-function and pointer-to-object [-Werror=pedantic]  
             sha.process_bytes( (unsigned char const*)&std::rand, sizeof( void(*)() ) );  
```

---

## Comment 1

> Username: ssmlp001  
> Created at: 2019-03-01 21:58:58 UTC  
> Url: https://github.com/boostorg/boost/issues/256#issuecomment-468826414  

sorry duplicate.
