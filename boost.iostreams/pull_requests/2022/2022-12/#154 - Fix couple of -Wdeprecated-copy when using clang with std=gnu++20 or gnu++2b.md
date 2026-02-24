# #154 Fix couple of -Wdeprecated-copy when using clang with std=gnu++20 or gnu++2b. [Open]

> Username: Romain-Geissler-1A  
> Created at: 2022-12-18 13:10:58 UTC  
> Updated at: 2022-12-18 13:10:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/154  

======= ERROR 1 ========  
```  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/stream_buffer.hpp:22: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:23: /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/adapter/concept_adapter.hpp:121:45: error: definition of implicit copy constructor for 'concept_adapter<boost::iost reams::back_insert_device<std::vector<char>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(concept_adapter& operator=(const concept_adapter&))  
                                            ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/optional.hpp:100:25: note: in implicit copy constructor for 'boost::iostreams::detail::concept_adapter<boost::iostr eams::back_insert_device<std::vector<char>>>' first required here  
        new (address()) T(t);  
                        ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:185:14: note: in instantiation of member function 'boost::iostreams::detail::optio nal<boost::iostreams::detail::concept_adapter<boost::iostreams::back_insert_device<std::vector<char>>>>::reset' requested here  
    storage_.reset(wrapper(t));  
             ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/stream_buffer.hpp:106:24: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::back_insert_device<std::vector<char>>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::open' requested here  
            base_type::open(t BOOST_IOSTREAMS_PUSH_ARGS());  
                       ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/stream_buffer.hpp:94:45: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::back_insert_device<std::vector<char>>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::open_impl' requested here  
    BOOST_IOSTREAMS_FORWARD( stream_buffer, open_impl, T,  
                                            ^  
src/Screen.cpp:1477:14: note: in instantiation of function template specialization 'boost::iostreams::stream_buffer<boost::iostreams::back_insert_device<std::vector<char>>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::stream_buffer<std::vector<char>>' requested here  
            :boost::iostreams::stream_buffer<boost::iostreams::back_insert_device<std::vector<char>>>(_storage)  
             ^  
```  
  
======= ERROR 2 ========  
```  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/stream_buffer.hpp:21: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/streambuf/direct_streambuf.hpp:24: /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/functional.hpp:118:52: error: definition of implicit copy constructor for 'member_close_operation<boost::iostreams::detail::linked_streambuf<char>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(member_close_operation& operator=(const member_close_operation&))  
                                                   ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:22:9: note: in implicit copy constructor for 'boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>' first required here  
        BOOST_PP_LOCAL_MACRO(3)  
        ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/execute.hpp:97:20: note: expanded from macro 'BOOST_PP_LOCAL_MACRO'  
                   op BOOST_PP_COMMA_IF(BOOST_PP_DEC(n)) \  
                   ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:202:13: note: in instantiation of function template specialization 'boost::iostreams::detail::execute_all<boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::back_insert_device<std::vector<char>>>>>, boost::iostreams::detail::clear_flags_operation<int>>' requested here  
    detail::execute_all(  
            ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/stream_buffer.hpp:91:23: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::back_insert_device<std::vector<char>>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::close' requested here  
                this->close();  
                      ^  
src/Screen.cpp:1476:9: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::back_insert_device<std::vector<char>>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::~stream_buffer' requested here  
        StreamBufferImplementation()  
        ^  
```  
  
======= ERROR 3 ========  
```  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/stream_buffer.hpp:21: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/streambuf/direct_streambuf.hpp:24: /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/functional.hpp:136:45: error: definition of implicit copy constructor for 'reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::back_insert_device<std::vector<char>>>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(reset_operation& operator=(const reset_operation&))  
                                            ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:22:9: note: in implicit copy constructor for 'boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::back_insert_device<std::vector<char>>>>>' first required here  
        BOOST_PP_LOCAL_MACRO(3)  
        ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/execute.hpp:98:20: note: expanded from macro 'BOOST_PP_LOCAL_MACRO'  
                   BOOST_PP_ENUM_PARAMS(BOOST_PP_DEC(n), c) \  
                   ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/preprocessor/repetition/enum_params.hpp:24:71: note: expanded from macro 'BOOST_PP_ENUM_PARAMS'  
                                                                      ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:202:13: note: in instantiation of function template specialization 'boost::iostreams::detail::execute_all<boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::back_insert_device<std::vector<char>>>>>, boost::iostreams::detail::clear_flags_operation<int>>' requested here  
    detail::execute_all(  
            ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/stream_buffer.hpp:91:23: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::back_insert_device<std::vector<char>>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::close' requested here  
                this->close();  
                      ^  
src/Screen.cpp:1476:9: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::back_insert_device<std::vector<char>>, std::char_traits<char>, std::allocator<char>, boost::iostreams::output>::~stream_buffer' requested here  
        StreamBufferImplementation()  
        ^  
```

---
