# #1133 - sanitizer warning [Open]

> Username: vinniefalco  
> Created at: 2025-12-30 20:52:58 UTC  
> Updated at: 2025-12-30 20:52:58 UTC  
> Url: https://github.com/boostorg/json/issues/1133  

https://github.com/cppalliance/http_proto/actions/runs/20605499846/job/59180338376  
  
Example:  
```  
  ./boost/json/detail/string_impl.hpp:338:23: error: 'key.boost::json::string::impl_.boost::json::detail::string_impl::<anonymous>.boost::json::detail::string_impl::<unnamed union>::p_.boost::json::detail::string_impl::pointer::t' may be used uninitialized [-Werror=maybe-uninitialized]  
    338 |             char*>(p_.t + 1);  
        |                    ~~~^  
  In file included from ./boost/json/src.hpp:39:  
  ./boost/json/impl/pointer.ipp: In function 'Value* boost::json::detail::walk_pointer(Value&, boost::json::string_view, boost::system::error_code&, OnObject, OnArray, OnScalar) [with Value = boost::json::value; OnObject = boost::json::value::set_at_pointer(boost::json::string_view, boost::json::value_ref, boost::system::error_code&, const boost::json::set_pointer_options&)::<lambda(boost::json::object&, pointer_token)>; OnArray = boost::json::value::set_at_pointer(boost::json::string_view, boost::json::value_ref, boost::system::error_code&, const boost::json::set_pointer_options&)::<lambda(boost::json::array&, std::size_t, boost::system::error_code&)>; OnScalar = boost::json::value::set_at_pointer(boost::json::string_view, boost::json::value_ref, boost::system::error_code&, const boost::json::set_pointer_options&)::<lambda(boost::json::value&, boost::json::string_view)>]':  
  ./boost/json/impl/pointer.ipp:438:20: note: 'key' declared here  
    438 |             string key( token.begin(), token.end(), obj.storage() );  
        |                    ^~~  
```
