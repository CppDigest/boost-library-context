# #365 - VS2010 incompatible issue [Closed]

> Username: CarlGitHub  
> Created at: 2020-10-16 01:35:11 UTC  
> Updated at: 2020-12-30 01:14:52 UTC  
> Closed at: 2020-12-30 01:14:51 UTC  
> Url: https://github.com/boostorg/asio/issues/365  

boost 1.74.0  
  
1>\boost_1_74_0\boost\asio\detail\impl\strand_executor_service.hpp(255): error C2244: 'boost::asio::detail::strand_executor_service::execute' : unable to match function definition to an existing declaration  
1>        definition  
1>        'void boost::asio::detail::strand_executor_service::execute(const boost::asio::detail::strand_executor_service::implementation_type &,Executor &,const Function &,enable_if<!boost::asio::can_query<Executor,boost::asio::execution::allocator_t<void>>::value>::type *)'  
1>        existing declarations  
1>        'void boost::asio::detail::strand_executor_service::execute(const boost::asio::detail::strand_executor_service::implementation_type &,Executor &,const Function &,enable_if<!boost::asio::can_query<Executor,boost::asio::execution::allocator_t<void>>::value>::type *)'  
1>        'void boost::asio::detail::strand_executor_service::execute(const boost::asio::detail::strand_executor_service::implementation_type &,Executor &,const Function &,enable_if<boost::asio::can_query<Executor,boost::asio::execution::allocator_t<void>>::value>::type *)'

---

## Comment 1

> Username: Rezasdemon  
> Created at: 2020-12-02 00:03:40 UTC  
> Url: https://github.com/boostorg/asio/issues/365#issuecomment-736898508  

Dependencies\boost_1_74_0\boost/asio/detail/impl/strand_executor_service.hpp(255): error C2244: 'boost::asio::detail::strand_executor_service::execute' : unable to match function definition to an existing declaration

---

## Comment 2

> Username: Ruffletuft  
> Created at: 2020-12-04 05:09:44 UTC  
> Url: https://github.com/boostorg/asio/issues/365#issuecomment-738568338  

Test boost1.74 with VS2010,same as op.   
Test boost1.68 with VS2010,ok.  
Test boost1.74 with VS2019,ok.  
I guess it's a compatibility issue.

---

## Comment 3

> Username: Rezasdemon  
> Created at: 2020-12-09 05:05:04 UTC  
> Url: https://github.com/boostorg/asio/issues/365#issuecomment-741529464  

yeah 1.7 was working for me on 2010 also

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:14:50 UTC  
> Url: https://github.com/boostorg/asio/issues/365#issuecomment-752293741  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#767](https://github.com/chriskohlhoff/asio/issues/767).
