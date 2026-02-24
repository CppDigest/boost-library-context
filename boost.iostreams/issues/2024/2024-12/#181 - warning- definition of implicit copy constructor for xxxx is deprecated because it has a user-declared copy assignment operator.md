# #181 - warning: definition of implicit copy constructor for xxxx is deprecated because it has a user-declared copy assignment operator [Open]

> Username: marakew  
> Created at: 2024-12-09 13:12:45 UTC  
> Updated at: 2024-12-09 13:12:45 UTC  
> Url: https://github.com/boostorg/iostreams/issues/181  

would be nice to fix this warn  
  
```  
In file included from D:\git\boost_\libs\iostreams\include\boost/iostreams/stream.hpp:21:  
In file included from D:\git\boost_\libs\iostreams\include\boost/iostreams/stream_buffer.hpp:21:  
In file included from D:\git\boost_\libs\iostreams\include\boost/iostreams/detail/streambuf/direct_streambuf.hpp:24:  
D:\git\boost_\libs\iostreams\include\boost/iostreams/detail/functional.hpp(136,45): warning: definition of implicit copy constructor for 'reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::file_descriptor_sink>>>' is deprecated because it has a user-declared copy assignment operator [-Wdeprecated-copy]  
  136 |     BOOST_DELETED_FUNCTION(reset_operation& operator=(const reset_operation&))  
      |                                             ^  
D:\git\boost_\libs\preprocessor\include\boost/preprocessor/iteration/detail/limits/local_256.hpp(22,9): note: in implicit copy constructor for 'boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::file_descriptor_sink>>>' first required here  
   22 |         BOOST_PP_LOCAL_MACRO(3)  
      |         ^  
D:\git\boost_\libs\iostreams\include\boost/iostreams/detail/execute.hpp(98,20): note: expanded from macro 'BOOST_PP_LOCAL_MACRO'  
   98 |                    BOOST_PP_ENUM_PARAMS(BOOST_PP_DEC(n), c) \  
      |                    ^  
D:\git\boost_\libs\preprocessor\include\boost\preprocessor\repetition\enum_params.hpp(24,71): note: expanded from macro 'BOOST_PP_ENUM_PARAMS'  
   24 | #    define BOOST_PP_ENUM_PARAMS(count, param) BOOST_PP_REPEAT(count, BOOST_PP_ENUM_PARAMS_M, param)  
      |                                                                       ^  
D:\git\boost_\libs\iostreams\include\boost/iostreams/detail/streambuf/indirect_streambuf.hpp(202,13): note: in instantiation of function template specialization 'boost::iostreams::detail::execute_all<boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::file_descriptor_sink>>>,  
 boost::iostreams::detail::clear_flags_operation<int>>' requested here  
  202 |     detail::execute_all(  
      |             ^  
D:\T60\git\boost_\libs\iostreams\include\boost/iostreams/stream_buffer.hpp(91,23): note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::file_descriptor_sink, std::char_traits<char>, std::allocator<char>, boost::iostreams::output_seekable>::close' requested here  
   91 |                 this->close();  
      |                       ^  
D:\git\boost_\libs\utility\include\boost/utility/base_from_member.hpp(125,7): note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::file_descriptor_sink, std::char_traits<char>, std::allocator<char>>::~stream_buffer' requested here  
  125 | class base_from_member  
      |       ^  
```
