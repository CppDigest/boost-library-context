# #76 - GCC suggest-override warnings [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-04 16:52:17 UTC  
> Updated at: 2020-04-14 19:50:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76  

GCC 7.5 fires suggest-override warnings in Boost 1.72:  
  
<pre>  
include/boost/smart_ptr/bad_weak_ptr.hpp:50:26: error: ‘virtual const char* boost::bad_weak_ptr::what() const’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/local_counted_base.hpp:116:18: error: ‘virtual void boost::detail::local_counted_impl::local_cb_destroy()’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/local_counted_base.hpp:121:41: error: ‘virtual boost::detail::shared_count boost::detail::local_counted_impl::local_cb_get_shared_count() const’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/local_counted_base.hpp:133:18: error: ‘virtual void boost::detail::local_counted_impl_em::local_cb_destroy()’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/local_counted_base.hpp:138:41: error: ‘virtual boost::detail::shared_count boost::detail::local_counted_impl_em::local_cb_get_shared_count() const’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:88:18: error: ‘void boost::detail::sp_counted_impl_p<X>::dispose() [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:96:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_deleter(const sp_typeinfo_&) [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:101:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_local_deleter(const sp_typeinfo_&) [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:106:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_untyped_deleter() [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’ can be marked override [-Werror=suggest-override]  
</pre>

---

## Comment 1

> Username: pdimov  
> Created at: 2020-04-05 17:17:31 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609450467  

I'm not seeing the first warning on Godbolt. https://godbolt.org/z/zxhehJ

---

## Comment 2

> Username: pdimov  
> Created at: 2020-04-05 17:18:48 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609450730  

Not seeing the second either: https://godbolt.org/z/b5Ouzs

---

## Comment 3

> Username: EugeneZelenko  
> Created at: 2020-04-05 21:56:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609491013  

Complete compilation log for problems in first two places:  
  
<pre>  
In file included from include/boost/smart_ptr/detail/shared_count.hpp:28:0,  
                 from include/boost/smart_ptr/shared_ptr.hpp:28,  
                 from include/boost/shared_ptr.hpp:17,  
                 from include/boost/python/converter/shared_ptr_to_python.hpp:12,  
                 from include/boost/python/converter/arg_to_python.hpp:15,  
                 from include/boost/python/call.hpp:15,  
                 from include/boost/python/object_core.hpp:14,  
                 from include/boost/python/object.hpp:9,  
                 from include/boost/python/import.hpp:8,  
                 from <Source file>:  
include/boost/smart_ptr/bad_weak_ptr.hpp:50:26: error: ‘virtual const char* boost::bad_weak_ptr::what() const’ can be marked override [-Werror=suggest-override]  
     virtual char const * what() const BOOST_NOEXCEPT_OR_NOTHROW  
                          ^~~~  
In file included from include/boost/smart_ptr/detail/local_sp_deleter.hpp:20:0,  
                 from include/boost/smart_ptr/shared_ptr.hpp:1158,  
                 from include/boost/shared_ptr.hpp:17,  
                 from include/boost/python/converter/shared_ptr_to_python.hpp:12,  
                 from include/boost/python/converter/arg_to_python.hpp:15,  
                 from include/boost/python/call.hpp:15,  
                 from include/boost/python/object_core.hpp:14,  
                 from include/boost/python/object.hpp:9,  
                 from include/boost/python/import.hpp:8,  
                 from <Source file>:  
include/boost/smart_ptr/detail/local_counted_base.hpp:116:18: error: ‘virtual void boost::detail::local_counted_impl::local_cb_destroy()’ can be marked override [-Werror=suggest-override]  
     virtual void local_cb_destroy() BOOST_SP_NOEXCEPT  
                  ^~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/local_counted_base.hpp:121:41: error: ‘virtual boost::detail::shared_count boost::detail::local_counted_impl::local_cb_get_shared_count() const’ can be marked override [-Werror=suggest-override]  
     virtual boost::detail::shared_count local_cb_get_shared_count() const BOOST_SP_NOEXCEPT  
                                         ^~~~~~~~~~~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/local_counted_base.hpp:133:18: error: ‘virtual void boost::detail::local_counted_impl_em::local_cb_destroy()’ can be marked override [-Werror=suggest-override]  
     virtual void local_cb_destroy() BOOST_SP_NOEXCEPT  
                  ^~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/local_counted_base.hpp:138:41: error: ‘virtual boost::detail::shared_count boost::detail::local_counted_impl_em::local_cb_get_shared_count() const’ can be marked override [-Werror=suggest-override]  
     virtual boost::detail::shared_count local_cb_get_shared_count() const BOOST_SP_NOEXCEPT  
                                         ^~~~~~~~~~~~~~~~~~~~~~~~~  
</pre>

---

## Comment 4

> Username: EugeneZelenko  
> Created at: 2020-04-05 22:07:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609492420  

Complete compilation log for problems in first and last place:  
  
<pre>  
In file included from include/boost/smart_ptr/detail/shared_count.hpp:28:0,  
                 from include/boost/smart_ptr/shared_ptr.hpp:28,  
                 from include/boost/shared_ptr.hpp:17,  
                 from include/boost/iostreams/filter/symmetric.hpp:61,  
                 from include/boost/iostreams/filter/bzip2.hpp:29,  
                 from <Source file>:  
include/boost/smart_ptr/bad_weak_ptr.hpp:50:26: error: ‘virtual const char* boost::bad_weak_ptr::what() const’ can be marked override [-Werror=suggest-override]  
     virtual char const * what() const BOOST_NOEXCEPT_OR_NOTHROW  
                          ^~~~  
In file included from include/boost/smart_ptr/detail/local_sp_deleter.hpp:20:0,  
                 from include/boost/smart_ptr/shared_ptr.hpp:1158,  
                 from include/boost/shared_ptr.hpp:17,  
                 from include/boost/iostreams/filter/symmetric.hpp:61,  
                 from include/boost/iostreams/filter/bzip2.hpp:29,  
                 from <Source file>:  
include/boost/smart_ptr/detail/local_counted_base.hpp:116:18: error: ‘virtual void boost::detail::local_counted_impl::local_cb_destroy()’ can be marked override [-Werror=suggest-override]  
     virtual void local_cb_destroy() BOOST_SP_NOEXCEPT  
                  ^~~~~~~~~~~~~~~~  
In file included from include/boost/smart_ptr/detail/shared_count.hpp:30:0,  
                 from include/boost/smart_ptr/shared_ptr.hpp:28,  
                 from include/boost/shared_ptr.hpp:17,  
                 from include/boost/iostreams/filter/symmetric.hpp:61,  
                 from include/boost/iostreams/filter/bzip2.hpp:29,  
                 from <Source file>:  
include/boost/smart_ptr/detail/sp_counted_impl.hpp: In instantiation of ‘class boost::detail::sp_counted_impl_p<boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl>’:  
include/boost/smart_ptr/detail/shared_count.hpp:145:19:   required from ‘boost::detail::shared_count::shared_count(Y*) [with Y = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’  
include/boost/smart_ptr/shared_ptr.hpp:285:20:   required from ‘void boost::detail::sp_pointer_construct(boost::shared_ptr<X>*, Y*, boost::detail::shared_count&) [with T = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; Y = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’  
include/boost/smart_ptr/shared_ptr.hpp:379:44:   required from ‘boost::shared_ptr<T>::shared_ptr(Y*) [with Y = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; T = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’  
include/boost/preprocessor/iteration/detail/local.hpp:37:9:   required from ‘boost::iostreams::symmetric_filter<SymmetricFilter, Alloc>::symmetric_filter(std::streamsize, const T0&) [with T0 = bool; SymmetricFilter = boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >; Alloc = std::allocator<char>; std::streamsize = long int]’  
include/boost/iostreams/filter/bzip2.hpp:419:35:   required from ‘boost::iostreams::basic_bzip2_decompressor<Alloc>::basic_bzip2_decompressor(bool, std::streamsize) [with Alloc = std::allocator<char>; std::streamsize = long int]’  
<Source file>:   required from here  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:88:18: error: ‘void boost::detail::sp_counted_impl_p<X>::dispose() [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’ can be marked override [-Werror=suggest-override]  
     virtual void dispose() BOOST_SP_NOEXCEPT  
                  ^~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:96:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_deleter(const sp_typeinfo_&) [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_deleter( sp_typeinfo_ const & ) BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:101:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_local_deleter(const sp_typeinfo_&) [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_local_deleter( sp_typeinfo_ const & ) BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:106:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_untyped_deleter() [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::bzip2_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_untyped_deleter() BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp: In instantiation of ‘class boost::detail::sp_counted_impl_p<boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl>’:  
include/boost/smart_ptr/detail/shared_count.hpp:145:19:   required from ‘boost::detail::shared_count::shared_count(Y*) [with Y = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’  
include/boost/smart_ptr/shared_ptr.hpp:285:20:   required from ‘void boost::detail::sp_pointer_construct(boost::shared_ptr<X>*, Y*, boost::detail::shared_count&) [with T = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; Y = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’  
include/boost/smart_ptr/shared_ptr.hpp:379:44:   required from ‘boost::shared_ptr<T>::shared_ptr(Y*) [with Y = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; T = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’  
include/boost/preprocessor/iteration/detail/local.hpp:37:9:   required from ‘boost::iostreams::symmetric_filter<SymmetricFilter, Alloc>::symmetric_filter(std::streamsize, const T0&) [with T0 = boost::iostreams::zlib_params; SymmetricFilter = boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >; Alloc = std::allocator<char>; std::streamsize = long int]’  
include/boost/iostreams/filter/zlib.hpp:420:31:   required from ‘boost::iostreams::basic_zlib_decompressor<Alloc>::basic_zlib_decompressor(const boost::iostreams::zlib_params&, std::streamsize) [with Alloc = std::allocator<char>; std::streamsize = long int]’  
include/boost/iostreams/filter/gzip.hpp:736:21:   required from ‘boost::iostreams::basic_gzip_decompressor<Alloc>::basic_gzip_decompressor(int, std::streamsize) [with Alloc = std::allocator<char>; std::streamsize = long int]’  
<Source file>:   required from here  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:88:18: error: ‘void boost::detail::sp_counted_impl_p<X>::dispose() [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’ can be marked override [-Werror=suggest-override]  
     virtual void dispose() BOOST_SP_NOEXCEPT  
                  ^~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:96:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_deleter(const sp_typeinfo_&) [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_deleter( sp_typeinfo_ const & ) BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:101:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_local_deleter(const sp_typeinfo_&) [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_local_deleter( sp_typeinfo_ const & ) BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:106:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_untyped_deleter() [with X = boost::iostreams::symmetric_filter<boost::iostreams::detail::zlib_decompressor_impl<std::allocator<char> >, std::allocator<char> >::impl]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_untyped_deleter() BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp: In instantiation of ‘class boost::detail::sp_counted_impl_p<boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl>’:  
include/boost/smart_ptr/detail/shared_count.hpp:145:19:   required from ‘boost::detail::shared_count::shared_count(Y*) [with Y = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl]’  
include/boost/smart_ptr/shared_ptr.hpp:285:20:   required from ‘void boost::detail::sp_pointer_construct(boost::shared_ptr<X>*, Y*, boost::detail::shared_count&) [with T = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl; Y = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl]’  
include/boost/smart_ptr/shared_ptr.hpp:379:44:   required from ‘boost::shared_ptr<T>::shared_ptr(Y*) [with Y = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl; T = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl]’  
include/boost/iostreams/chain.hpp:131:41:   required from ‘boost::iostreams::detail::chain_base<Self, Ch, Tr, Alloc, Mode>::chain_base() [with Self = boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >; Ch = char; Tr = std::char_traits<char>; Alloc = std::allocator<char>; Mode = boost::iostreams::input]’  
include/boost/iostreams/chain.hpp:431:1:   required from ‘boost::iostreams::chain<Mode, Ch, Tr, Alloc>::chain() [with Mode = boost::iostreams::input; Ch = char; Tr = std::char_traits<char>; Alloc = std::allocator<char>]’  
include/boost/iostreams/detail/streambuf/chainbuf.hpp:53:16:   required from ‘boost::iostreams::detail::chainbuf<Chain, Access, Mode>::chainbuf() [with Chain = boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >; Mode = boost::iostreams::input; Access = boost::iostreams::public_]’  
include/boost/iostreams/filtering_streambuf.hpp:60:1:   required from ‘boost::iostreams::filtering_streambuf<Mode, Ch, Tr, Alloc, Access>::filtering_streambuf() [with Mode = boost::iostreams::input; Ch = char; Tr = std::char_traits<char>; Alloc = std::allocator<char>; Access = boost::iostreams::public_]’  
<Source file>:   required from here  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:88:18: error: ‘void boost::detail::sp_counted_impl_p<X>::dispose() [with X = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl]’ can be marked override [-Werror=suggest-override]  
     virtual void dispose() BOOST_SP_NOEXCEPT  
                  ^~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:96:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_deleter(const sp_typeinfo_&) [with X = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_deleter( sp_typeinfo_ const & ) BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:101:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_local_deleter(const sp_typeinfo_&) [with X = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_local_deleter( sp_typeinfo_ const & ) BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~~~~~~~  
include/boost/smart_ptr/detail/sp_counted_impl.hpp:106:20: error: ‘void* boost::detail::sp_counted_impl_p<X>::get_untyped_deleter() [with X = boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input, char, std::char_traits<char>, std::allocator<char> >, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl]’ can be marked override [-Werror=suggest-override]  
     virtual void * get_untyped_deleter() BOOST_SP_NOEXCEPT  
                    ^~~~~~~~~~~~~~~~~~~  
</pre>

---

## Comment 5

> Username: pdimov  
> Created at: 2020-04-05 22:16:16 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609493555  

Is there a program that I can compile that reproduces the warnings?

---

## Comment 6

> Username: EugeneZelenko  
> Created at: 2020-04-05 22:43:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609497185  

Somehow I was not able to reproduce it on godbold.  
  
However I looked on first occurrence and warning looks reasonable for me:  
  
<pre>  
class bad_weak_ptr: public std::exception  
{  
public:  
  
    virtual char const * what() const BOOST_NOEXCEPT_OR_NOTHROW  
    {  
        return "tr1::bad_weak_ptr";  
    }  
};  
</pre>  
  
`what()` is virtual in [base class](https://en.cppreference.com/w/cpp/error/exception/what).  
  
Same is true for `local_cb_destroy()` and `local_cb_get_shared_count()` in `local_counted_impl` and `local_counted_base`

---

## Comment 7

> Username: EugeneZelenko  
> Created at: 2020-04-05 22:55:28 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609498738  

Clang-tidy [modernize-use-override](https://clang.llvm.org/extra/clang-tidy/checks/modernize-use-override.html) should also complain about such cases.

---

## Comment 8

> Username: pdimov  
> Created at: 2020-04-05 22:56:27 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-609498873  

Godbolt probably includes Boost headers with -isystem.

---

## Comment 9

> Username: EugeneZelenko  
> Created at: 2020-04-08 22:54:30 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-611235152  

Related: boostorg/config#253

---

## Comment 10

> Username: EugeneZelenko  
> Created at: 2020-04-12 18:34:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-612657886  

Complete list of warnings when Boost is built with `-Wsuggest-override`added to `cxxflags`. Duplicated warnings from same location is omitted:  
  
<pre>  
./boost/smart_ptr/bad_weak_ptr.hpp:50:26: warning: ‘virtual const char* boost::bad_weak_ptr::what() const’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/local_counted_base.hpp:116:18: warning: ‘virtual void boost::detail::local_counted_impl::local_cb_destroy()’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/local_counted_base.hpp:121:41: warning: ‘virtual boost::detail::shared_count boost::detail::local_counted_impl::local_cb_get_shared_count() const’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/local_counted_base.hpp:133:18: warning: ‘virtual void boost::detail::local_counted_impl_em::local_cb_destroy()’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/local_counted_base.hpp:138:41: warning: ‘virtual boost::detail::shared_count boost::detail::local_counted_impl_em::local_cb_get_shared_count() const’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/shared_count.hpp:634:67: warning: potential null pointer dereference [-Wnull-dereference]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:101:20: warning: ‘void* boost::detail::sp_counted_impl_p<X>::get_local_deleter(const sp_typeinfo_&) [with X = boost::basic_wrap_stringstream<char>; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:106:20: warning: ‘void* boost::detail::sp_counted_impl_p<X>::get_untyped_deleter() [with X = boost::basic_wrap_stringstream<char>]’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:171:18: warning: ‘void boost::detail::sp_counted_impl_pd<P, D>::dispose() [with P = boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char>*; D = boost::detail::sp_ms_deleter<boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char> >]’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:176:20: warning: ‘void* boost::detail::sp_counted_impl_pd<P, D>::get_deleter(const sp_typeinfo_&) [with P = boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char>*; D = boost::detail::sp_ms_deleter<boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char> >; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:181:20: warning: ‘void* boost::detail::sp_counted_impl_pd<P, D>::get_local_deleter(const sp_typeinfo_&) [with P = boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char>*; D = boost::detail::sp_ms_deleter<boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char> >; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:186:20: warning: ‘void* boost::detail::sp_counted_impl_pd<P, D>::get_untyped_deleter() [with P = boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char>*; D = boost::detail::sp_ms_deleter<boost::log::v2_mt_posix::{anonymous}::default_console_sink_factory<char> >]’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:88:18: warning: ‘void boost::detail::sp_counted_impl_p<X>::dispose() [with X = boost::basic_wrap_stringstream<char>]’ can be marked override [-Wsuggest-override]  
./boost/smart_ptr/detail/sp_counted_impl.hpp:96:20: warning: ‘void* boost::detail::sp_counted_impl_p<X>::get_deleter(const sp_typeinfo_&) [with X = boost::basic_wrap_stringstream<char>; boost::detail::sp_typeinfo_ = std::type_info]’ can be marked override [-Wsuggest-override]  
</pre>

---

## Comment 11

> Username: pdimov  
> Created at: 2020-04-12 18:56:29 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-612660617  

This one is interesting.  
```  
./boost/smart_ptr/detail/shared_count.hpp:634:67: warning: potential null pointer dereference [-Wnull-dereference]  
```

---

## Comment 12

> Username: glenfe  
> Created at: 2020-04-13 14:54:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-612932869  

Waiting on boostorg/config#329

---

## Comment 13

> Username: pdimov  
> Created at: 2020-04-13 21:54:40 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-613116667  

Building what library cases the -Wnull-dereference warning?

---

## Comment 14

> Username: EugeneZelenko  
> Created at: 2020-04-13 22:10:27 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-613122310  

This is excerpt from build log file:  
  
<pre>  
gcc.compile.c++ bin.v2/libs/log/build/gcc-7.5.0/release/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o  
  
    "gcc-7.5.0/bin/g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fPIC -isystem /usr/include/x86_64-linux-gnu -DBOOST_NO_AUTO_PTR -Wextra -Wdangling-else -Wdisabled-optimization -Wduplicated-branches -Wduplicated-cond -Wcast-align -Wcast-qual -Wlogical-op -Wnull-dereference -Wrestrict -Wshift-negative-value -Wshift-overflow -Wsuggest-override -fno-strict-aliasing -ftemplate-depth-1024 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_HAS_ICU=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DDATE_TIME_INLINE -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src" -c -o "bin.v2/libs/log/build/gcc-7.5.0/release/threadapi-pthread/threading-multi/visibility-hidden/text_file_backend.o" "libs/log/src/text_file_backend.cpp"  
  
...  
  
In file included from ./boost/smart_ptr/shared_ptr.hpp:28:0,  
                 from ./boost/smart_ptr/make_shared_object.hpp:17,  
                 from libs/log/src/text_file_backend.cpp:35:  
./boost/smart_ptr/detail/shared_count.hpp: In function ‘boost::shared_ptr<boost::log::v2_mt_posix::sinks::file::collector> boost::log::v2_mt_posix::sinks::file::aux::make_collector(const boost::filesystem::path&, uintmax_t, uintmax_t, uintmax_t)’:  
./boost/smart_ptr/detail/shared_count.hpp:634:67: warning: potential null pointer dereference [-Wnull-dereference]  
 inline shared_count::shared_count( weak_count const & r ): pi_( r.pi_ )  
                                                                 ~~^~~  
</pre>

---

## Comment 15

> Username: pdimov  
> Created at: 2020-04-14 13:11:41 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-613433877  

The override warnings should be fixed in develop.

---

## Comment 16

> Username: EugeneZelenko  
> Created at: 2020-04-14 14:45:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/76#issuecomment-613485940  

Thank you for help! Please also delete `virtual` in overridden methods to keep Clang-tidy's [modernize-use-override](https://clang.llvm.org/extra/clang-tidy/checks/modernize-use-override.html) happy.
