# #333 - Warnings about usage of uninitialized data in `wchar_from_mb` [Open]

> Username: Lastique  
> Created at: 2025-09-06 22:55:26 UTC  
> Updated at: 2025-09-06 22:55:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/333  

Building Boost 1.89.0 with the following command line:  
  
```  
b2 -j 16 --toolset=gcc cxxflags="-flto=auto -ffat-lto-objects -march=nehalem -mtune=icelake-server -faligned-new -fcoroutines -fno-plt -fvisibility-inlines-hidden -O3 -fno-math-errno -fno-rounding-math -fno-signaling-nans -fno-trapping-math -ftree-vectorize -fgcse-sm -fgcse-las -fgcse-after-reload -ftree-loop-im -ftree-loop-distribution -ftree-loop-if-convert -funswitch-loops -frename-registers -fivopts" linkflags="-Wl,-z,relro -Wl,-z,now -Wl,-O1 -Wl,--hash-style=both -Wl,-z,noexecstack -Wl,--as-needed" \  
 variant=release cxxstd=20-gnu threading=multi debug-symbols=on runtime-link=shared optimization=speed inlining=full warnings=on link=shared --without-mpi --without-wave --without-python --without-graph stage  
```  
  
generates the following warning:  
  
```  
In file included from ./boost/archive/impl/xml_woarchive_impl.ipp:42,  
                 from libs/serialization/src/xml_woarchive.cpp:28:  
./boost/archive/iterators/wchar_from_mb.hpp: In instantiation of ‘boost::archive::iterators::wchar_from_mb<Base>::sliding_buffer<T>::sliding_buffer() [with T = char; Base = boost::archive::iterators::xml_escape<const char*>]’:  
./boost/archive/iterators/wchar_from_mb.hpp:131:31:   required from ‘boost::archive::iterators::wchar_from_mb<Base>::wchar_from_mb(T) [with T = const char*; Base = boost::archive::iterators::xml_escape<const char*>]’  
./boost/archive/impl/xml_woarchive_impl.ipp:59:9:   required from ‘void boost::archive::save_iterator(std::wostream&, InputIterator, InputIterator) [with InputIterator = const char*; std::wostream = std::basic_ostream<wchar_t>]’  
./boost/archive/impl/xml_woarchive_impl.ipp:100:17:   required from ‘void boost::archive::xml_woarchive_impl<Archive>::save(const char*) [with Archive = boost::archive::xml_woarchive]’  
libs/serialization/src/xml_woarchive.cpp:35:16:   required from here  
./boost/archive/iterators/wchar_from_mb.hpp:103:30: warning: member ‘boost::archive::iterators::wchar_from_mb<boost::archive::iterators::xml_escape<const char*> >::sliding_buffer<char>::m_buffer’ is used uninitialized [-Wuninitialized]  
  103 |             m_next_available(m_buffer.begin()),  
      |                              ^~~~~~~~  
./boost/archive/iterators/wchar_from_mb.hpp: In instantiation of ‘boost::archive::iterators::wchar_from_mb<Base>::sliding_buffer<T>::sliding_buffer() [with T = wchar_t; Base = boost::archive::iterators::xml_escape<const char*>]’:  
./boost/archive/iterators/wchar_from_mb.hpp:131:31:   required from ‘boost::archive::iterators::wchar_from_mb<Base>::wchar_from_mb(T) [with T = const char*; Base = boost::archive::iterators::xml_escape<const char*>]’  
./boost/archive/impl/xml_woarchive_impl.ipp:59:9:   required from ‘void boost::archive::save_iterator(std::wostream&, InputIterator, InputIterator) [with InputIterator = const char*; std::wostream = std::basic_ostream<wchar_t>]’  
./boost/archive/impl/xml_woarchive_impl.ipp:100:17:   required from ‘void boost::archive::xml_woarchive_impl<Archive>::save(const char*) [with Archive = boost::archive::xml_woarchive]’  
libs/serialization/src/xml_woarchive.cpp:35:16:   required from here  
./boost/archive/iterators/wchar_from_mb.hpp:103:30: warning: member ‘boost::archive::iterators::wchar_from_mb<boost::archive::iterators::xml_escape<const char*> >::sliding_buffer<wchar_t>::m_buffer’ is used uninitialized [-Wuninitialized]  
```  
  
This is on Kubuntu 24.04 using gcc 13.3.0.  
  
Judging by the [code](https://github.com/boostorg/serialization/blob/8a8c62864f05732131bf6785d54466d8ac6cd477/include/boost/archive/iterators/wchar_from_mb.hpp#L97-L106), `m_buffer` is indeed uninitialized by the constructor, but it is a `boost::array` and `m_next_available` initializer only takes an iterator from it, so the warning is probably harmless. Still, the warning should be silenced in one way or another.
