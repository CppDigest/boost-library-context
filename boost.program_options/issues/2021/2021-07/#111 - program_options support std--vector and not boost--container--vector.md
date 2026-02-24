# #111 - program_options support std::vector and not boost::container::vector [Open]

> Username: zBeeble42  
> Created at: 2021-07-13 20:55:33 UTC  
> Updated at: 2021-12-15 07:19:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/111  

It may seem rather obvious, but boost::program_options doesn't appear to support boost::container::vector in the manner it supports std::vector.  An an example, creating an option:  
  
`      ("test,T", po::value<boost::container::vector<std::string> >(),"Run a named test suite.  -T <suite>:<suite arguments>")  
`  
will cause clang to say:  
```  
  
In file included from rogue.c++:6:  
In file included from /usr/local/include/boost/program_options.hpp:15:  
In file included from /usr/local/include/boost/program_options/options_description.hpp:13:  
In file included from /usr/local/include/boost/program_options/value_semantic.hpp:14:  
In file included from /usr/local/include/boost/lexical_cast.hpp:32:  
In file included from /usr/local/include/boost/lexical_cast/try_lexical_convert.hpp:44:  
/usr/local/include/boost/lexical_cast/detail/converter_lexical.hpp:243:13: error: static_assert  
      failed due to requirement 'has_right_shift<std::istream, boost::container::vector<std::string, void, void>,  
      boost::binary_op_detail::dont_care>::value || boost::has_right_shift<std::wistream,  
      boost::container::vector<std::string, void, void>, boost::binary_op_detail::dont_care>::value' "Target type is  
      neither std::istream`able nor std::wistream`able"  
  ...BOOST_STATIC_ASSERT_MSG((result_t::value || boost::has_right_shift<std::basic_istream<wchar_t>, T >::value),  
     ^                        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
```  
  
... among other "nice" things.  I've tried creating an istream for boost::container::vector and prototyping it before including program_options, but either I am not worthy or it's not possible for me to easily retrofit.  
  
... seems like either program_options needs to make the istream  optional (I'm certainly not using it) or convince boost::container::vector to include it.  Dunno.
