# #6 fix for the non-MSVC case on Windows [Merged]

> Username: tabe  
> Created at: 2015-08-21 02:31:52 UTC  
> Updated at: 2015-08-25 02:52:08 UTC  
> Merged at: 2015-08-25 02:52:08 UTC  
> Closed at: 2015-08-25 02:52:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/6  

... such as MinGW, with which g++ generates the following errors during building client code using boost::uuids::basic_random_generator:  
  
> In file included from /foo/bar/include/boost/uuid/random_generator.hpp:12:0,  
>                  from /foo/bar/include/boost/uuid/uuid_generators.hpp:17,  
>                  from ../../../../src/uuidgen.h:8,  
>                  from ../../../../src/baz/quux.cc:28:  
> /foo/bar/include/boost/uuid/seed_rng.hpp:240:20: error: 'winapi' in namespace 'boost::detail' does not name a type  
>      boost::detail::winapi::HCRYPTPROV_ random_;  
>                     ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp: In constructor 'boost::uuids::detail::seed_rng::seed_rng()':  
> /foo/bar/include/boost/uuid/seed_rng.hpp:89:11: error: class 'boost::uuids::detail::seed_rng' does not have any field named 'random_'  
>          , random_(NULL)  
>            ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:92:29: error: 'boost::detail::winapi' has not been declared  
>          if (!boost::detail::winapi::CryptAcquireContextA(  
>                              ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:93:22: error: 'random_' was not declared in this scope  
>                      &random_,  
>                       ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:96:36: error: 'boost::detail::winapi' has not been declared  
>                      boost::detail::winapi::PROV_RSA_FULL_,  
>                                     ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:97:36: error: 'boost::detail::winapi' has not been declared  
>                      boost::detail::winapi::CRYPT_VERIFYCONTEXT_ | boost::detail::winapi::CRYPT_SILENT_))  
>                                     ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:97:82: error: 'boost::detail::winapi' has not been declared  
>                      boost::detail::winapi::CRYPT_VERIFYCONTEXT_ | boost::detail::winapi::CRYPT_SILENT_))  
>                                                                                   ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp: In destructor 'boost::uuids::detail::seed_rng::~seed_rng()':  
> /foo/bar/include/boost/uuid/seed_rng.hpp:110:13: error: 'random_' was not declared in this scope  
>          if (random_) {  
>              ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:112:28: error: 'boost::detail::winapi' has not been declared  
>              boost::detail::winapi::CryptReleaseContext(random_, 0);  
>                             ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp: In member function 'void boost::uuids::detail::seed_rng::sha1_random_digest_()':  
> /foo/bar/include/boost/uuid/seed_rng.hpp:155:13: error: 'random_' was not declared in this scope  
>          if (random_)  
>              ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:160:28: error: 'boost::detail::winapi' has not been declared  
>              boost::detail::winapi::CryptGenRandom(random_, sizeof(state), state);  
>                             ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:170:28: error: 'boost::detail::winapi' has not been declared  
>              boost::detail::winapi::DWORD_ procid = boost::detail::winapi::GetCurrentProcessId();  
>                             ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:171:55: error: 'procid' was not declared in this scope  
>              sha.process_bytes( (unsigned char const_)&procid, sizeof( procid ) );  
>                                                        ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:173:28: error: 'boost::detail::winapi' has not been declared  
>              boost::detail::winapi::DWORD_ threadid = boost::detail::winapi::GetCurrentThreadId();  
>                             ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:174:55: error: 'threadid' was not declared in this scope  
>              sha.process_bytes( (unsigned char const_)&threadid, sizeof( threadid ) );  
>                                                        ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:176:28: error: 'boost::detail::winapi' has not been declared  
>              boost::detail::winapi::LARGE_INTEGER_ ts;  
>                             ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:177:13: error: 'ts' was not declared in this scope  
>              ts.QuadPart = 0;  
>              ^  
> /foo/bar/include/boost/uuid/seed_rng.hpp:178:28: error: 'boost::detail::winapi' has not been declared  
>              boost::detail::winapi::QueryPerformanceCounter( &ts );  
>                             ^

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-08-21 09:54:03 UTC  
> Url: https://github.com/boostorg/uuid/pull/6#issuecomment-133358613  

You are assuming that all compilers on Windows have the VC++ compatible #pragmas. That is not necessarily the case.

---

## Comment 2

> Username: tabe  
> Created_at: 2015-08-22 02:05:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/6#issuecomment-133626103  

@eldiener, thanks for your comment.  
Yes, my initial attempt was careless of other compilers' features.  
Even worse, it missed the match of warning(push)/warning(pop).

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-08-22 02:17:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/6#issuecomment-133626669  

The #pragma comment line should go within the first _MSC_VER check. Whether Windows or not, as long as it is _MSC_VER the #pragma comment should be OK.

---
