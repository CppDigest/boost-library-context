# #121 - Clang-tidy performance-* warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-05-06 15:59:51 UTC  
> Updated at: 2020-05-07 17:41:00 UTC  
> Closed at: 2020-05-07 17:39:14 UTC  
> Url: https://github.com/boostorg/log/issues/121  

I run Clang-tidy 10 performance-* checks and found next warnings in current code:  
  
<pre>  
./boost/log/sources/severity_logger.hpp:104:47: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_logger.hpp:104:5: warning: move assignment operators should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_logger.hpp:126:47: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_logger.hpp:126:5: warning: move assignment operators should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_logger.hpp:56:47: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_logger.hpp:56:5: warning: move assignment operators should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_logger.hpp:78:47: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_logger.hpp:78:5: warning: move assignment operators should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/detail/enqueued_record.hpp:69:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/detail/enqueued_record.hpp:85:22: warning: move assignment operators should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/detail/format.hpp:97:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/basic_logger.hpp:158:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/basic_logger.hpp:356:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/basic_logger.hpp:533:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_feature.hpp:105:9: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_feature.hpp:126:25: warning: move assignment operators should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/sources/severity_feature.hpp:214:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/utility/setup/settings.hpp:587:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
./boost/log/utility/setup/settings.hpp:621:21: warning: move assignment operators should be marked noexcept [performance-noexcept-move-constructor]  
libs/log/src/named_scope_format_parser.cpp:598:5: warning: move constructors should be marked noexcept [performance-noexcept-move-constructor]  
libs/log/src/text_file_backend.cpp:777:26: warning: the variable 'file_name' is copy-constructed from a const reference but is only used as const reference; consider making it a const reference [performance-unnecessary-copy-initialization]  
</pre>  
  
However, I'm not sure what is right way to fix `performance-noexcept-move-constructor` ones. My guess is that `BOOST_NOEXCEPT` should be used.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-05-07 17:40:00 UTC  
> Url: https://github.com/boostorg/log/issues/121#issuecomment-625398840  

Some logger functions (including move assignment) are necessarily non-noexcept because they use potentially throwing operations internally.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-05-07 17:41:00 UTC  
> Url: https://github.com/boostorg/log/issues/121#issuecomment-625399358  

Thank you for quick fix!
