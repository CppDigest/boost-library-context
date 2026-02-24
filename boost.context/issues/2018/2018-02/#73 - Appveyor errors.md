# #73 - Appveyor errors [Closed]

> Username: vinniefalco  
> Created at: 2018-02-21 21:30:54 UTC  
> Updated at: 2018-02-24 07:45:12 UTC  
> Closed at: 2018-02-24 07:45:12 UTC  
> Url: https://github.com/boostorg/context/issues/73  

I get this error with the latest Boost.Context now:  
```  
C:\projects\boost-root\boost/context/execution_context_v1.hpp(60): error C2492: 'boost::context::detail::ecv1_activation_record::current_rec': data with thread storage duration may not have dll interface  
C:\projects\boost-root\boost/context/execution_context_v1.hpp(60): warning C4251: 'boost::context::detail::ecv1_activation_record::current_rec': class 'boost::intrusive_ptr<boost::context::detail::ecv1_activation_record>' needs to have dll-interface to be used by clients of struct 'boost::context::detail::ecv1_activation_record'  
C:\projects\boost-root\boost/context/execution_context_v1.hpp(58): note: see declaration of 'boost::intrusive_ptr<boost::context::detail::ecv1_activation_record>'  
C:\projects\boost-root\boost/context/execution_context_v1.hpp(60): error C2492: 'public: static boost::intrusive_ptr<boost::context::detail::ecv1_activation_record> boost::context::detail::ecv1_activation_record::current_rec': data with thread storage duration may not have dll interface  
```

---

## Comment 1

> Username: olk  
> Created at: 2018-02-24 07:45:12 UTC  
> Url: https://github.com/boostorg/context/issues/73#issuecomment-368208604  

fixed, ty ... please note that execution_context v1 is deprecated (does not prevent UB)
