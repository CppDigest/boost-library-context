# #968 - Linux gcc compilation failures with --pedantic-errors flag: error: extra ‘;’ [Closed]

> Username: nigels-com  
> Created at: 2024-01-05 13:04:51 UTC  
> Updated at: 2024-01-08 11:45:54 UTC  
> Closed at: 2024-01-08 11:45:54 UTC  
> Url: https://github.com/boostorg/json/issues/968  

### Version of Boost  
  
1.84.0  
  
### Steps necessary to reproduce the problem  
  
Ubuntu 22.04, g++ 11.4.0  
  
### Example  
  
`--pedantic-errors` worked fine for Boost 1.83.0 boost::json, and previous versions of Boost.  
  
It would be be nice to have this fixed for a possible 1.83.1, this is a blocker for us,  `--pedantic-errors` mode is strongly encouraged internally.  (Rightly or wrongly)  
  
```  
$ g++ -c validate.cpp -I$(pwd)/../include -I$(pwd)/../tmp/boost_1_84_0 --pedantic-errors  
In file included from /home/nigels/dev/boost-json/example/../include/boost/json/parse_into.hpp:19,  
                 from /home/nigels/dev/boost-json/example/../include/boost/json.hpp:26,  
                 from validate.cpp:16:  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:870:47: error: extra ‘;’ [-Wpedantic]  
  870 |     BOOST_JSON_HANDLE_EVENT( on_object_begin );  
      |                                               ^  
      |                                               -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:871:45: error: extra ‘;’ [-Wpedantic]  
  871 |     BOOST_JSON_HANDLE_EVENT( on_object_end );  
      |                                             ^  
      |                                             -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:882:10: error: extra ‘;’ [-Wpedantic]  
  882 |         };  
      |          ^  
      |          -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:913:10: error: extra ‘;’ [-Wpedantic]  
  913 |         };  
      |          ^  
      |          -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:929:43: error: extra ‘;’ [-Wpedantic]  
  929 |     BOOST_JSON_HANDLE_EVENT( on_key_part );  
      |                                           ^  
      |                                           -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:930:38: error: extra ‘;’ [-Wpedantic]  
  930 |     BOOST_JSON_HANDLE_EVENT( on_key );  
      |                                      ^  
      |                                      -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:931:46: error: extra ‘;’ [-Wpedantic]  
  931 |     BOOST_JSON_HANDLE_EVENT( on_string_part );  
      |                                              ^  
      |                                              -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:932:41: error: extra ‘;’ [-Wpedantic]  
  932 |     BOOST_JSON_HANDLE_EVENT( on_string );  
      |                                         ^  
      |                                         -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:933:46: error: extra ‘;’ [-Wpedantic]  
  933 |     BOOST_JSON_HANDLE_EVENT( on_number_part );  
      |                                              ^  
      |                                              -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:934:40: error: extra ‘;’ [-Wpedantic]  
  934 |     BOOST_JSON_HANDLE_EVENT( on_int64 );  
      |                                        ^  
      |                                        -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:935:41: error: extra ‘;’ [-Wpedantic]  
  935 |     BOOST_JSON_HANDLE_EVENT( on_uint64 );  
      |                                         ^  
      |                                         -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:936:41: error: extra ‘;’ [-Wpedantic]  
  936 |     BOOST_JSON_HANDLE_EVENT( on_double );  
      |                                         ^  
      |                                         -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:937:39: error: extra ‘;’ [-Wpedantic]  
  937 |     BOOST_JSON_HANDLE_EVENT( on_bool );  
      |                                       ^  
      |                                       -  
/home/nigels/dev/boost-json/example/../include/boost/json/detail/parse_into.hpp:938:39: error: extra ‘;’ [-Wpedantic]  
  938 |     BOOST_JSON_HANDLE_EVENT( on_null );  
      |                                       ^  
      |                                       -  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-01-08 11:45:54 UTC  
> Url: https://github.com/boostorg/json/issues/968#issuecomment-1880847776  

Fixed in 942bbd4cf5e5fd30b7be71d187ab5bd2e54f8939
