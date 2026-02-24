# #12 - Shouldn't BOOST_NO_EXCEPTIONS define throw_exception with BOOST_NORETURN? [Closed]

> Username: shanepowell  
> Created at: 2018-11-20 20:21:51 UTC  
> Updated at: 2019-06-03 12:45:50 UTC  
> Closed at: 2019-06-03 12:45:50 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/12  

Defining throw_exception with BOOST_NORETURN will (at least for MSVC) stop the compiling from issuing warnings about nothing returned from some boost includes.  These warnings don't happen with BOOST_NORETURN not defines throw_exception with BOOST_NORETURN.  
  
An example of a boost include that will generate this warning is:  
boost\iostreams\detail\streambuf\direct_streambuf.hpp(165): warning C4715: 'boost::iostreams::detail::direct_streambuf<boost::iostreams::basic_array_source<char>,std::char_traits<char> >::pbackfail': not all control paths return a value  
  
because it's implemented like this:  
template<typename T, typename Tr>  
typename direct_streambuf<T, Tr>::int_type   
direct_streambuf<T, Tr>::pbackfail(int_type c)  
{  
    using namespace std;  
    if (!ibeg_)   
        boost::throw_exception(cant_read());  
    if (gptr() != 0 && gptr() != ibeg_) {  
        gbump(-1);  
        if (!traits_type::eq_int_type(c, traits_type::eof()))  
            *gptr() = traits_type::to_char_type(c);  
        return traits_type::not_eof(c);  
    }  
    boost::throw_exception(bad_putback());  
}
