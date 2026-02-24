# #201 - clang-15 warning: definition of implicit copy assignment operator for 'value_ref<jb::severity_level>' is deprecated because it has a user-declared copy constructor [Closed]

> Username: k15tfu  
> Created at: 2022-12-21 23:26:53 UTC  
> Updated at: 2022-12-22 12:00:15 UTC  
> Closed at: 2022-12-22 12:00:15 UTC  
> Url: https://github.com/boostorg/log/issues/201  

Hi!  
  
In file boost/log/utility/value_ref.hpp:  
```  
boost/log/utility/value_ref.hpp:455:30: error: definition of implicit copy assignment operator for 'value_ref<jb::severity_level>' is deprecated because it has a user-declared copy constructor [-Werror,-Wdeprecated-copy]  
    BOOST_DEFAULTED_FUNCTION(value_ref(value_ref const& that), BOOST_NOEXCEPT : base_type(static_cast< base_type const& >(that)) {})  
                             ^  
[...]  
3 errors generated.  
```
