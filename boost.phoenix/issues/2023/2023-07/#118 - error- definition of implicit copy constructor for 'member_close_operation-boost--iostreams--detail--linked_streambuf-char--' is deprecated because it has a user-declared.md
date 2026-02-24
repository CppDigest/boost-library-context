# #118 - error: definition of implicit copy constructor for 'member_close_operation<boost::iostreams::detail::linked_streambuf<char>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy] [Open]

> Username: kshailes94  
> Created at: 2023-07-23 11:16:40 UTC  
> Updated at: 2023-07-23 11:17:12 UTC  
> Url: https://github.com/boostorg/phoenix/issues/118  

Happneing on mac m1   
  
In file included from tools/common/ToolsCommon.cpp:15:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_stream.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_streambuf.hpp:17:  
In file included from /opt/homebrew/include/boost/iostreams/chain.hpp:34:  
In file included from /opt/homebrew/include/boost/iostreams/stream_buffer.hpp:21:  
In file included from /opt/homebrew/include/boost/iostreams/detail/streambuf/direct_streambuf.hpp:24:  
/opt/homebrew/include/boost/iostreams/detail/functional.hpp:118:52: error: definition of implicit copy constructor for 'member_close_operation<boost::iostreams::detail::linked_streambuf<char>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(member_close_operation& operator=(const member_close_operation&))  
                                                   ^  
/opt/homebrew/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:22:9: note: in implicit copy constructor for 'boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>' first required here  
        BOOST_PP_LOCAL_MACRO(3)  
        ^  
/opt/homebrew/include/boost/iostreams/detail/execute.hpp:97:20: note: expanded from macro 'BOOST_PP_LOCAL_MACRO'  
                   op BOOST_PP_COMMA_IF(BOOST_PP_DEC(n)) \  
                   ^  
/opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:202:13: note: in instantiation of function template specialization 'boost::iostreams::detail::execute_all<boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::basic_null_device<char, boost::iostreams::input>>>>, boost::iostreams::detail::clear_flags_operation<int>>' requested here  
    detail::execute_all(  
            ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:91:23: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::basic_null_device<char, boost::iostreams::input>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::close' requested here  
                this->close();   
                      ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:328:66: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::basic_null_device<char, boost::iostreams::input>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::~stream_buffer' requested here  
                    stream_buffer< basic_null_device<Ch, Mode> > null;  
                                                                 ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:321:23: note: in instantiation of member function 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl::close' requested here  
                try { close(); } catch (...) { }  
                      ^  
/opt/homebrew/include/boost/core/checked_delete.hpp:36:5: note: (skipping 4 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
    delete x;  
    ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:131:20: note: in instantiation of function template specialization 'boost::shared_ptr<boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl>::shared_ptr<boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl>' requested here  
    chain_base() : pimpl_(new chain_impl) { }  
                   ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:431:28: note: in instantiation of member function 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_base' requested here  
BOOST_IOSTREAMS_DECL_CHAIN(chain, char)  
                           ^  
/opt/homebrew/include/boost/iostreams/filtering_stream.hpp:96:5: note: in instantiation of member function 'boost::iostreams::chain<boost::iostreams::input>::chain' requested here  
    filtering_stream_base() : stream_type(0) { this->set_chain(&chain_); }  
    ^  
/opt/homebrew/include/boost/iostreams/filtering_stream.hpp:169:38: note: in instantiation of member function 'boost::iostreams::detail::filtering_stream_base<boost::iostreams::chain<boost::iostreams::input>, boost::iostreams::public_>::filtering_stream_base' requested here  
BOOST_IOSTREAMS_DEFINE_FILTER_STREAM(filtering_stream, boost::iostreams::chain, char)  
                                     ^  
tools/common/ToolsCommon.cpp:211:39: note: in instantiation of member function 'boost::iostreams::filtering_stream<boost::iostreams::input>::filtering_stream' requested here  
  boost::iostreams::filtering_istream inbuf;  
                                      ^  
In file included from tools/common/ToolsCommon.cpp:15:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_stream.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_streambuf.hpp:17:  
In file included from /opt/homebrew/include/boost/iostreams/chain.hpp:34:  
In file included from /opt/homebrew/include/boost/iostreams/stream_buffer.hpp:21:  
In file included from /opt/homebrew/include/boost/iostreams/detail/streambuf/direct_streambuf.hpp:24:  
/opt/homebrew/include/boost/iostreams/detail/functional.hpp:136:45: error: definition of implicit copy constructor for 'reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::basic_null_device<char, boost::iostreams::input>>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(reset_operation& operator=(const reset_operation&))  
                                            ^  
/opt/homebrew/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:22:9: note: in implicit copy constructor for 'boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::basic_null_device<char, boost::iostreams::input>>>>' first required here  
        BOOST_PP_LOCAL_MACRO(3)  
        ^  
/opt/homebrew/include/boost/iostreams/detail/execute.hpp:98:20: note: expanded from macro 'BOOST_PP_LOCAL_MACRO'  
                   BOOST_PP_ENUM_PARAMS(BOOST_PP_DEC(n), c) \  
                   ^  
/opt/homebrew/include/boost/preprocessor/repetition/enum_params.hpp:24:71: note: expanded from macro 'BOOST_PP_ENUM_PARAMS'  
#    define BOOST_PP_ENUM_PARAMS(count, param) BOOST_PP_REPEAT(count, BOOST_PP_ENUM_PARAMS_M, param)  
                                                                      ^  
/opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:202:13: note: in instantiation of function template specialization 'boost::iostreams::detail::execute_all<boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::basic_null_device<char, boost::iostreams::input>>>>, boost::iostreams::detail::clear_flags_operation<int>>' requested here  
    detail::execute_all(  
            ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:91:23: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::basic_null_device<char, boost::iostreams::input>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::close' requested here  
                this->close();   
                      ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:328:66: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::basic_null_device<char, boost::iostreams::input>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::~stream_buffer' requested here  
                    stream_buffer< basic_null_device<Ch, Mode> > null;  
                                                                 ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:321:23: note: in instantiation of member function 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl::close' requested here  
                try { close(); } catch (...) { }  
                      ^  
/opt/homebrew/include/boost/core/checked_delete.hpp:36:5: note: (skipping 4 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
    delete x;  
    ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:131:20: note: in instantiation of function template specialization 'boost::shared_ptr<boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl>::shared_ptr<boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl>' requested here  
    chain_base() : pimpl_(new chain_impl) { }  
                   ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:431:28: note: in instantiation of member function 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_base' requested here  
BOOST_IOSTREAMS_DECL_CHAIN(chain, char)  
                           ^  
/opt/homebrew/include/boost/iostreams/filtering_stream.hpp:96:5: note: in instantiation of member function 'boost::iostreams::chain<boost::iostreams::input>::chain' requested here  
    filtering_stream_base() : stream_type(0) { this->set_chain(&chain_); }  
    ^  
/opt/homebrew/include/boost/iostreams/filtering_stream.hpp:169:38: note: in instantiation of member function 'boost::iostreams::detail::filtering_stream_base<boost::iostreams::chain<boost::iostreams::input>, boost::iostreams::public_>::filtering_stream_base' requested here  
BOOST_IOSTREAMS_DEFINE_FILTER_STREAM(filtering_stream, boost::iostreams::chain, char)  
                                     ^  
tools/common/ToolsCommon.cpp:211:39: note: in instantiation of member function 'boost::iostreams::filtering_stream<boost::iostreams::input>::filtering_stream' requested here  
  boost::iostreams::filtering_istream inbuf;  
                                      ^  
In file included from tools/common/ToolsCommon.cpp:15:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_stream.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_streambuf.hpp:17:  
In file included from /opt/homebrew/include/boost/iostreams/chain.hpp:34:  
In file included from /opt/homebrew/include/boost/iostreams/stream_buffer.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:23:  
/opt/homebrew/include/boost/iostreams/detail/adapter/concept_adapter.hpp:121:45: error: definition of implicit copy constructor for 'concept_adapter<boost::iostreams::basic_null_device<char, boost::iostreams::input>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(concept_adapter& operator=(const concept_adapter&))  
                                            ^  
/opt/homebrew/include/boost/iostreams/detail/optional.hpp:100:25: note: in implicit copy constructor for 'boost::iostreams::detail::concept_adapter<boost::iostreams::basic_null_device<char, boost::iostreams::input>>' first required here  
        new (address()) T(t);   
                        ^  
/opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:185:14: note: in instantiation of member function 'boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::basic_null_device<char, boost::iostreams::input>>>::reset' requested here  
    storage_.reset(wrapper(t));  
             ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:106:24: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::basic_null_device<char, boost::iostreams::input>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::open' requested here  
            base_type::open(t BOOST_IOSTREAMS_PUSH_ARGS());  
                       ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:94:45: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::basic_null_device<char, boost::iostreams::input>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::open_impl' requested here  
    BOOST_IOSTREAMS_FORWARD( stream_buffer, open_impl, T,  
                                            ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:330:30: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::basic_null_device<char, boost::iostreams::input>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::open' requested here  
                        null.open(basic_null_device<Ch, Mode>());  
                             ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:321:23: note: (skipping 5 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
                try { close(); } catch (...) { }  
                      ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:131:20: note: in instantiation of function template specialization 'boost::shared_ptr<boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl>::shared_ptr<boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_impl>' requested here  
    chain_base() : pimpl_(new chain_impl) { }  
                   ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:431:28: note: in instantiation of member function 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::chain_base' requested here  
BOOST_IOSTREAMS_DECL_CHAIN(chain, char)  
                           ^  
/opt/homebrew/include/boost/iostreams/filtering_stream.hpp:96:5: note: in instantiation of member function 'boost::iostreams::chain<boost::iostreams::input>::chain' requested here  
    filtering_stream_base() : stream_type(0) { this->set_chain(&chain_); }  
    ^  
/opt/homebrew/include/boost/iostreams/filtering_stream.hpp:169:38: note: in instantiation of member function 'boost::iostreams::detail::filtering_stream_base<boost::iostreams::chain<boost::iostreams::input>, boost::iostreams::public_>::filtering_stream_base' requested here  
BOOST_IOSTREAMS_DEFINE_FILTER_STREAM(filtering_stream, boost::iostreams::chain, char)  
                                     ^  
tools/common/ToolsCommon.cpp:211:39: note: in instantiation of member function 'boost::iostreams::filtering_stream<boost::iostreams::input>::filtering_stream' requested here  
  boost::iostreams::filtering_istream inbuf;  
                                      ^  
In file included from tools/common/ToolsCommon.cpp:15:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_stream.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_streambuf.hpp:17:  
In file included from /opt/homebrew/include/boost/iostreams/chain.hpp:34:  
In file included from /opt/homebrew/include/boost/iostreams/stream_buffer.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:23:  
/opt/homebrew/include/boost/iostreams/detail/adapter/concept_adapter.hpp:121:45: error: definition of implicit copy constructor for 'concept_adapter<CommentFilter>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(concept_adapter& operator=(const concept_adapter&))  
                                            ^  
/opt/homebrew/include/boost/iostreams/detail/optional.hpp:100:25: note: in implicit copy constructor for 'boost::iostreams::detail::concept_adapter<CommentFilter>' first required here  
        new (address()) T(t);   
                        ^  
/opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:185:14: note: in instantiation of member function 'boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<CommentFilter>>::reset' requested here  
    storage_.reset(wrapper(t));  
             ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:106:24: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<CommentFilter, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::open' requested here  
            base_type::open(t BOOST_IOSTREAMS_PUSH_ARGS());  
                       ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:94:45: note: in instantiation of member function 'boost::iostreams::stream_buffer<CommentFilter, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::open_impl' requested here  
    BOOST_IOSTREAMS_FORWARD( stream_buffer, open_impl, T,  
                                            ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:254:21: note: in instantiation of member function 'boost::iostreams::stream_buffer<CommentFilter, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::stream_buffer' requested here  
            buf(new streambuf_t(t, buffer_size, pback_size));  
                    ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:205:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push_impl<CommentFilter>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:496:15: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push<CommentFilter>' requested here  
    { chain_->push(t BOOST_IOSTREAMS_PUSH_ARGS()); }  
              ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:484:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push_impl<CommentFilter>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
tools/common/ToolsCommon.cpp:212:9: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push<CommentFilter>' requested here  
  inbuf.push(CommentFilter());  
        ^  
In file included from tools/common/ToolsCommon.cpp:15:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_stream.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_streambuf.hpp:17:  
In file included from /opt/homebrew/include/boost/iostreams/chain.hpp:34:  
In file included from /opt/homebrew/include/boost/iostreams/stream_buffer.hpp:21:  
In file included from /opt/homebrew/include/boost/iostreams/detail/streambuf/direct_streambuf.hpp:24:  
/opt/homebrew/include/boost/iostreams/detail/functional.hpp:136:45: error: definition of implicit copy constructor for 'reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<CommentFilter>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(reset_operation& operator=(const reset_operation&))  
                                            ^  
/opt/homebrew/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:22:9: note: in implicit copy constructor for 'boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<CommentFilter>>>' first required here  
        BOOST_PP_LOCAL_MACRO(3)  
        ^  
/opt/homebrew/include/boost/iostreams/detail/execute.hpp:98:20: note: expanded from macro 'BOOST_PP_LOCAL_MACRO'  
                   BOOST_PP_ENUM_PARAMS(BOOST_PP_DEC(n), c) \  
                   ^  
/opt/homebrew/include/boost/preprocessor/repetition/enum_params.hpp:24:71: note: expanded from macro 'BOOST_PP_ENUM_PARAMS'  
#    define BOOST_PP_ENUM_PARAMS(count, param) BOOST_PP_REPEAT(count, BOOST_PP_ENUM_PARAMS_M, param)  
                                                                      ^  
/opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:202:13: note: in instantiation of function template specialization 'boost::iostreams::detail::execute_all<boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<CommentFilter>>>, boost::iostreams::detail::clear_flags_operation<int>>' requested here  
    detail::execute_all(  
            ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:91:23: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<CommentFilter, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::close' requested here  
                this->close();   
                      ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:94:30: note: in instantiation of member function 'boost::iostreams::stream_buffer<CommentFilter, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::~stream_buffer' requested here  
    BOOST_IOSTREAMS_FORWARD( stream_buffer, open_impl, T,  
                             ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:254:21: note: in instantiation of member function 'boost::iostreams::stream_buffer<CommentFilter, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::stream_buffer' requested here  
            buf(new streambuf_t(t, buffer_size, pback_size));  
                    ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:205:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push_impl<CommentFilter>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:496:15: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push<CommentFilter>' requested here  
    { chain_->push(t BOOST_IOSTREAMS_PUSH_ARGS()); }  
              ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:484:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push_impl<CommentFilter>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
tools/common/ToolsCommon.cpp:212:9: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push<CommentFilter>' requested here  
  inbuf.push(CommentFilter());  
        ^  
In file included from tools/common/ToolsCommon.cpp:15:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_stream.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_streambuf.hpp:17:  
In file included from /opt/homebrew/include/boost/iostreams/chain.hpp:34:  
In file included from /opt/homebrew/include/boost/iostreams/stream_buffer.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:23:  
/opt/homebrew/include/boost/iostreams/detail/adapter/concept_adapter.hpp:121:45: error: definition of implicit copy constructor for 'concept_adapter<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(concept_adapter& operator=(const concept_adapter&))  
                                            ^  
/opt/homebrew/include/boost/iostreams/detail/optional.hpp:100:25: note: in implicit copy constructor for 'boost::iostreams::detail::concept_adapter<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' first required here  
        new (address()) T(t);   
                        ^  
/opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:185:14: note: in instantiation of member function 'boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>>::reset' requested here  
    storage_.reset(wrapper(t));  
             ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:106:24: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::open' requested here  
            base_type::open(t BOOST_IOSTREAMS_PUSH_ARGS());  
                       ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:94:45: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::open_impl' requested here  
    BOOST_IOSTREAMS_FORWARD( stream_buffer, open_impl, T,  
                                            ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:254:21: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::stream_buffer' requested here  
            buf(new streambuf_t(t, buffer_size, pback_size));  
                    ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:205:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push_impl<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:496:15: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' requested here  
    { chain_->push(t BOOST_IOSTREAMS_PUSH_ARGS()); }  
              ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:484:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push_impl<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
tools/common/ToolsCommon.cpp:213:9: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push<char, std::char_traits<char>>' requested here  
  inbuf.push(config_stream);  
        ^  
In file included from tools/common/ToolsCommon.cpp:15:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_stream.hpp:22:  
In file included from /opt/homebrew/include/boost/iostreams/filtering_streambuf.hpp:17:  
In file included from /opt/homebrew/include/boost/iostreams/chain.hpp:34:  
In file included from /opt/homebrew/include/boost/iostreams/stream_buffer.hpp:21:  
In file included from /opt/homebrew/include/boost/iostreams/detail/streambuf/direct_streambuf.hpp:24:  
/opt/homebrew/include/boost/iostreams/detail/functional.hpp:136:45: error: definition of implicit copy constructor for 'reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
    BOOST_DELETED_FUNCTION(reset_operation& operator=(const reset_operation&))  
                                            ^  
/opt/homebrew/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:22:9: note: in implicit copy constructor for 'boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>>>' first required here  
        BOOST_PP_LOCAL_MACRO(3)  
        ^  
/opt/homebrew/include/boost/iostreams/detail/execute.hpp:98:20: note: expanded from macro 'BOOST_PP_LOCAL_MACRO'  
                   BOOST_PP_ENUM_PARAMS(BOOST_PP_DEC(n), c) \  
                   ^  
/opt/homebrew/include/boost/preprocessor/repetition/enum_params.hpp:24:71: note: expanded from macro 'BOOST_PP_ENUM_PARAMS'  
#    define BOOST_PP_ENUM_PARAMS(count, param) BOOST_PP_REPEAT(count, BOOST_PP_ENUM_PARAMS_M, param)  
                                                                      ^  
/opt/homebrew/include/boost/iostreams/detail/streambuf/indirect_streambuf.hpp:202:13: note: in instantiation of function template specialization 'boost::iostreams::detail::execute_all<boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::member_close_operation<boost::iostreams::detail::linked_streambuf<char>>, boost::iostreams::detail::reset_operation<boost::iostreams::detail::optional<boost::iostreams::detail::concept_adapter<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>>>, boost::iostreams::detail::clear_flags_operation<int>>' requested here  
    detail::execute_all(  
            ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:91:23: note: in instantiation of member function 'boost::iostreams::detail::indirect_streambuf<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::close' requested here  
                this->close();   
                      ^  
/opt/homebrew/include/boost/iostreams/stream_buffer.hpp:94:30: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::~stream_buffer' requested here  
    BOOST_IOSTREAMS_FORWARD( stream_buffer, open_impl, T,  
                             ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:254:21: note: in instantiation of member function 'boost::iostreams::stream_buffer<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::stream_buffer' requested here  
            buf(new streambuf_t(t, buffer_size, pback_size));  
                    ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:205:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push_impl<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:496:15: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_base<boost::iostreams::chain<boost::iostreams::input>, char, std::char_traits<char>, std::allocator<char>, boost::iostreams::input>::push<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' requested here  
    { chain_->push(t BOOST_IOSTREAMS_PUSH_ARGS()); }  
              ^  
/opt/homebrew/include/boost/iostreams/chain.hpp:484:56: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push_impl<boost::iostreams::detail::mode_adapter<boost::iostreams::input, std::istream>>' requested here  
    BOOST_IOSTREAMS_DEFINE_PUSH(push, mode, char_type, push_impl)  
                                                       ^  
tools/common/ToolsCommon.cpp:213:9: note: in instantiation of function template specialization 'boost::iostreams::detail::chain_client<boost::iostreams::chain<boost::iostreams::input>>::push<char, std::char_traits<char>>' requested here  
  inbuf.push(config_stream);  
        ^  
7 errors generated.  
make[2]: *** [tools/common/redex_all-ToolsCommon.o] Error 1  
make[1]: *** [all-recursive] Error 1  
make: *** [all] Error 2
