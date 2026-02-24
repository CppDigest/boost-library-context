# #555 - compile warnings with -Wconversion enabled [Open]

> Username: brigam001  
> Created at: 2021-09-06 14:21:05 UTC  
> Updated at: 2021-09-06 14:55:21 UTC  
> Url: https://github.com/boostorg/boost/issues/555  

Compiling with g++ version 9.3 on ubuntu 20.04  
with -Wconversion warning enabled  
we get compiler warnings when #including any of the following files:  
  
            boost/crc.hpp  
            boost/date_time/microsec_time_clock.hpp  
            boost/date_time/wrapping_int.hpp  
            boost/optional/optional.hpp  
            boost/throw_exception.hpp  
            boost/token_functions.hpp  
            boost/tuple/tuple.hpp  
            boost/type_traits/is_complete.hpp  
            boost/uuid/string_generator.hpp  
  
I'm using BOOST version 1.77.0  
but the problem is present with older versions too    
  
Example: file main.cc  
  
#include <boost/crc.hpp>  
  
int main()  
{  
    return 0;  
}  
  
Then compile with:  
                 g++ -std=c++17 -Wconversion -g -IpathToBoostIncludes  main.cc  
  
Then we get the following warnings:  
  
In file included from include/boost/array.hpp:50,  
                 from include/boost/crc.hpp:39,  
                 from main.cc:4:  
include/boost/throw_exception.hpp:54:36: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   54 | template<class E, class B, int I = sizeof( wrapexcept_is_convertible<B>( static_cast< E* >( 0 ) ) ) > struct wrapexcept_add_base;  
      |                                    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
include/boost/throw_exception.hpp:54:36: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
In file included from main.cc:4:  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:73:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
   73 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly = 0u,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:74:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
   74 |            BOOST_CRC_PARM_TYPE InitRem = 0u,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:75:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
   75 |            BOOST_CRC_PARM_TYPE FinalXor = 0u, bool ReflectIn = false,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:80:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
   80 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:81:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
   81 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:81:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
   81 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:83:25: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   83 |     typename uint_t<Bits>::fast  crc( void const *buffer,  
      |                         ^  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:87:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
   87 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly >  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:88:25: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   88 |     typename uint_t<Bits>::fast  augmented_crc( void const *buffer,  
      |                         ^  
include/boost/crc.hpp:90:26: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   90 |      typename uint_t<Bits>::fast initial_remainder = 0u);  
      |                          ^  
include/boost/crc.hpp:155:40: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
  155 |     typedef typename boost::uint_t<Bits>::fast  value_type;  
      |                                        ^  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:241:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
  241 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:242:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
  242 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:242:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
  242 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:249:40: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
  249 |     typedef typename boost::uint_t<Bits>::fast  value_type;  
      |                                        ^  
include/boost/crc.hpp:308:55: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
  308 |     typedef detail::possible_reflector<Bits, ReflectIn>     reflect_i_type;  
      |                                                       ^  
include/boost/crc.hpp:309:58: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
  309 |     typedef detail::crc_driver<Bits, TruncPoly, ReflectIn>  crc_table_type;  
      |                                                          ^  
include/boost/crc.hpp:310:69: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
  310 |     typedef detail::possible_reflector<Bits, ReflectRem != ReflectIn>  
      |                                                                     ^  
include/boost/crc.hpp: In function 'unsigned char boost::detail::reflect_sub_byte(unsigned char, int)':  
include/boost/crc.hpp:456:30: warning: conversion from 'int' to 'unsigned char' may change value [-Wconversion]  
  456 |     { return reflect_byte(x) >> (CHAR_BIT - word_length); }  
      |              ~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp: In member function 'boost::crc_basic<Bits>::value_type boost::crc_basic<Bits>::get_interim_remainder() const':  
include/boost/crc.hpp:1679:52: warning: conversion from 'std::size_t' {aka 'long unsigned int'} to 'int' may change value [-Wconversion]  
 1679 |     return rem_ & detail::low_bits_mask_c<bit_count>::value;  
      |                                                    ^  
include/boost/crc.hpp: In member function 'boost::crc_basic<Bits>::value_type boost::crc_basic<Bits>::checksum() const':  
include/boost/crc.hpp:1896:58: warning: conversion from 'std::size_t' {aka 'long unsigned int'} to 'int' may change value [-Wconversion]  
 1896 |      rem_) ^ final_ ) & detail::low_bits_mask_c<bit_count>::value;  
      |                                                          ^  
include/boost/crc.hpp: At global scope:  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1922:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1922 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1923:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1923 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1923:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1923 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1935:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1935 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1936:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1936 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1936:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1936 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1948:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1948 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1949:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1949 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1949:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1949 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1961:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1961 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1962:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1962 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1962:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1962 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1974:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1974 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1975:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1975 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1975:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1975 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1987:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1987 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1988:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1988 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:1988:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 1988 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2000:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2000 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2001:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2001 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2001:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2001 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp: In member function 'boost::crc_optimal<Bits, TruncPoly, InitRem, FinalXor, ReflectIn, ReflectRem>::value_type boost::crc_optimal<Bits, TruncPoly, InitRem, FinalXor, ReflectIn, ReflectRem>::get_interim_remainder() const':  
include/boost/crc.hpp:2011:39: warning: conversion from 'std::size_t' {aka 'long unsigned int'} to 'int' may change value [-Wconversion]  
 2011 |      detail::low_bits_mask_c<bit_count>::value;  
      |                                       ^  
include/boost/crc.hpp: At global scope:  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2028:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2028 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2029:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2029 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2029:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2029 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2047:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2047 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2048:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2048 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2048:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2048 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2066:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2066 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2067:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2067 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2067:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2067 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2087:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2087 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2088:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2088 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2088:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2088 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2103:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2103 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2104:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2104 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2104:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2104 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp: In member function 'boost::crc_optimal<Bits, TruncPoly, InitRem, FinalXor, ReflectIn, ReflectRem>::value_type boost::crc_optimal<Bits, TruncPoly, InitRem, FinalXor, ReflectIn, ReflectRem>::checksum() const':  
include/boost/crc.hpp:2113:41: warning: conversion from 'std::size_t' {aka 'long unsigned int'} to 'int' may change value [-Wconversion]  
 2113 |      & detail::low_bits_mask_c<bit_count>::value;  
      |                                         ^  
include/boost/crc.hpp: At global scope:  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2135:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2135 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2136:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2136 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2136:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2136 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2164:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2164 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2165:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2165 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2165:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2165 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2216:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2216 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly,  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2217:12: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2217 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |            ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2217:41: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2217 |            BOOST_CRC_PARM_TYPE InitRem, BOOST_CRC_PARM_TYPE FinalXor,  
      |                                         ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:2220:21: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
 2220 | typename uint_t<Bits>::fast  
      |                     ^  
include/boost/crc.hpp:57:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
   57 | #define BOOST_CRC_PARM_TYPE  typename ::boost::uint_t<Bits>::fast  
      |                                                           ^  
include/boost/crc.hpp:2281:30: note: in expansion of macro 'BOOST_CRC_PARM_TYPE'  
 2281 | template < std::size_t Bits, BOOST_CRC_PARM_TYPE TruncPoly >  
      |                              ^~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:2282:21: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
 2282 | typename uint_t<Bits>::fast  
      |                     ^  
include/boost/crc.hpp:2287:25: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
 2287 |     typename uint_t<Bits>::fast  initial_remainder  // = 0u  
      |                         ^  
include/boost/crc.hpp: In function 'typename boost::uint_t<Bits>::fast boost::augmented_crc(const void*, std::size_t, typename boost::uint_t<Bits>::fast)':  
include/boost/crc.hpp:2290:40: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
 2290 |     return detail::low_bits_mask_c<Bits>::value &  
      |                                        ^  
include/boost/crc.hpp:2291:59: warning: conversion from 'long unsigned int' to 'int' may change value [-Wconversion]  
 2291 |      detail::byte_table_driven_crcs<Bits, TruncPoly, false>::  
      |                                                           ^  
include/boost/crc.hpp: In instantiation of 'Unsigned boost::detail::reflect_unsigned(Unsigned, int) [with Unsigned = unsigned char]':  
include/boost/crc.hpp:415:47:   required from here  
include/boost/crc.hpp:387:39: warning: conversion from 'int' to 'unsigned char' may change value [-Wconversion]  
  387 |         for ( Unsigned  l = 1u, h = l << (word_length - 1) ; h > l ; h >>= 1, l  
      |                                     ~~^~~~~~~~~~~~~~~~~~~~  
include/boost/crc.hpp:387:72: warning: conversion from 'int' to 'unsigned char' may change value [-Wconversion]  
  387 |         for ( Unsigned  l = 1u, h = l << (word_length - 1) ; h > l ; h >>= 1, l  
      |                                                                      ~~^~~~~  
include/boost/crc.hpp:388:10: warning: conversion from 'int' to 'unsigned char' may change value [-Wconversion]  
  387 |         for ( Unsigned  l = 1u, h = l << (word_length - 1) ; h > l ; h >>= 1, l  
      |                                                                               ~  
  388 |          <<= 1 )  
      |          ^~~~~
