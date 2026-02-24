# #4 - boost::coroutine2<>::pull_type does not accept std::bind [Closed]

> Username: ipapadop  
> Created at: 2016-10-24 13:01:07 UTC  
> Updated at: 2017-01-21 13:12:23 UTC  
> Closed at: 2017-01-13 12:20:23 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/4  

I have the following small example of generating ints in https://gist.github.com/ipapadop/08722cf2033f018a273a1d7114164da7  
  
In Boost.Coroutine, pull_type (line 68) accepts std::bind, whereas with Boost.Coroutine2 (Boost 1.62) I'm getting an error:  
  
`/usr/local/boost-1.62/include/boost/context/execution_context_v2.hpp:141:23: error: no matching function for call to ‘apply(std::remove_reference<std::_Bind<boost::coroutines2::detail::pull_coroutine<T>::control_block::control_block(boost::context::preallocated, StackAllocator, Fn&&) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = std::_Bind<void (*(std::_Placeholder<1>, unsigned int, unsigned int, int))(boost::coroutines2::detail::push_coroutine<int>&, unsigned int, int, int)>; T = int]::<lambda(std::decay<std::_Bind<void (*(std::_Placeholder<1>, unsigned int, unsigned int, int))(boost::coroutines2::detail::push_coroutine<int>&, unsigned int, int, int)> >::type&, boost::context::execution_context<int*>, int*)>(std::_Bind<void (*(std::_Placeholder<1>, unsigned int, unsigned int, int))(boost::coroutines2::detail::push_coroutine<int>&, unsigned int, int, int)>, std::_Placeholder<1>, std::_Placeholder<2>)>&>::type, std::remove_reference<std::tuple<boost::context::execution_context<int*>&&, int*>&>::type)’`

---

## Comment 1

> Username: olk  
> Created at: 2016-10-26 11:54:03 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/4#issuecomment-256325232  

maybe a problem of apply()  (boost.context) - I've to look at it

---

## Comment 2

> Username: olk  
> Created at: 2017-01-13 12:20:23 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/4#issuecomment-272430899  

it's an issue of invoke from boost.context - I close this bug and open one in boost.context

---

## Comment 3

> Username: olk  
> Created at: 2017-01-21 13:12:23 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/4#issuecomment-274260904  

fixed - was a problem inside boost.coroutine2
