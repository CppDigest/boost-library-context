# #342 - Forward declaration of container::basic_string no longer valid [Closed]

> Username: pdimov  
> Created at: 2026-01-24 15:25:22 UTC  
> Updated at: 2026-01-24 20:42:41 UTC  
> Closed at: 2026-01-24 20:42:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/342  

```  
gcc.compile.c++ bin.v2/libs/log/build/gcc-11/release/x86_64/link-static/threading-multi/visibility-hidden/text_file_backend.o  
In file included from ./boost/lexical_cast/detail/converter_lexical.hpp:47,  
                 from ./boost/lexical_cast/try_lexical_convert.hpp:37,  
                 from ./boost/lexical_cast.hpp:39,  
                 from ./boost/date_time/format_date_parser.hpp:14,  
                 from ./boost/date_time/date_generator_parser.hpp:20,  
                 from ./boost/date_time/date_facet.hpp:25,  
                 from ./boost/date_time/gregorian/gregorian_io.hpp:16,  
                 from ./boost/date_time/gregorian/gregorian.hpp:31,  
                 from ./boost/date_time/posix_time/time_formatters.hpp:12,  
                 from ./boost/date_time/posix_time/posix_time.hpp:24,  
                 from /home/runner/work/boost/boost/libs/log/src/text_file_backend.cpp:50:  
./boost/container/container_fwd.hpp:278:7: error: redeclared with 4 template parameters  
  278 | class basic_string;  
      |       ^~~~~~~~~~~~  
In file included from ./boost/filesystem/path.hpp:34,  
                 from ./boost/filesystem/directory.hpp:20,  
                 from /home/runner/work/boost/boost/libs/log/src/text_file_backend.cpp:42:  
./boost/filesystem/detail/path_traits.hpp:45:48: note: previous declaration ‘template<class, class, class> class boost::container::basic_string’ used 3 template parameters  
   45 | template< typename, typename, typename > class basic_string;  
      |                                                ^~~~~~~~~~~~  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-24 15:37:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/342#issuecomment-3794826516  

See https://github.com/boostorg/filesystem/pull/343
