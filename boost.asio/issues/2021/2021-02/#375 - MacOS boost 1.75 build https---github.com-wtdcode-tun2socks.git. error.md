# #375 - MacOS  boost 1.75  build https://github.com/wtdcode/tun2socks.git.    error [Closed]

> Username: dylu6699  
> Created at: 2021-02-15 15:05:54 UTC  
> Updated at: 2021-05-19 01:40:24 UTC  
> Closed at: 2021-05-19 01:40:23 UTC  
> Url: https://github.com/boostorg/asio/issues/375  

source:  
tun2socks-w/socks5/socks5_client.cpp:45  
    boost::asio::spawn(this->strand_,  
                       boost::bind(&SOCKS5Client::startOnSOCKS5Thread, this,  
                                   boost::placeholders::_1));  
  
  
Scanning dependencies of target tun2socks  
[ 57%] Building CXX object CMakeFiles/tun2socks.dir/socks5/socks5_client.cpp.o  
In file included from /Users/dylu/Documents/20210211/tun2socks-w/socks5/socks5_client.cpp:1:  
In file included from /Users/dylu/Documents/20210211/tun2socks-w/./socks5/socks5_client.h:8:  
In file included from /Users/dylu/Documents/20210211/boost_1_75_0/boost/asio.hpp:38:  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/bind_executor.hpp:180:7: error: no matching constructor for initialization of  
      'boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >'  
    : executor_(BOOST_ASIO_MOVE_CAST(E)(e)),  
      ^         ~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/bind_executor.hpp:320:7: note: in instantiation of function template specialization 'boost::asio::detail::executor_binder_base<void (*)(),  
      boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >, false>::executor_binder_base<boost::asio::io_context::strand, void (*const &)()>' requested here  
    : base_type(other.get_executor(), other.get())  
      ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/spawn.hpp:110:7: note: in instantiation of function template specialization 'boost::asio::executor_binder<void (*)(),  
      boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > >::executor_binder<void (*)(), boost::asio::io_context::strand>' requested here  
      handler_(other.handler_),  
      ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/bind/bind.hpp:297:55: note: in instantiation of function template specialization 'boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(),  
      boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > >::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::io_context::strand> >'  
      requested here  
        unwrapper<F>::unwrap(f, 0)(a[base_type::a1_], a[base_type::a2_]);  
                                                      ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/bind/bind.hpp:1284:16: note: in instantiation of function template specialization 'boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1>  
      >::operator()<boost::_mfi::mf1<void, toys::socks5::SOCKS5Client, boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(),  
      boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >, boost::_bi::rrlist1<const boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(),  
      boost::asio::io_context::strand> > &> >' requested here  
        return l_( type<result_type>(), f_, a, 0 );  
               ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/impl/spawn.hpp:356:7: note: in instantiation of function template specialization 'boost::_bi::bind_t<void, boost::_mfi::mf1<void, toys::socks5::SOCKS5Client,  
      boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,  
      boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >,  
      boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1> > >::operator()<const boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::io_context::strand> >  
      &>' requested here  
      (data->function_)(yield);  
      ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/coroutine/detail/push_coroutine_object.hpp:302:11: note: (skipping 13 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
        { fn_( push_coro); }  
          ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/async_result.hpp:152:5: note: in instantiation of function template specialization  
      'boost::asio::detail::initiate_dispatch::operator()<boost::asio::detail::spawn_helper<boost::asio::executor_binder<void (*)(), boost::asio::io_context::strand>, boost::_bi::bind_t<void, boost::_mfi::mf1<void,  
      toys::socks5::SOCKS5Client, boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,  
      boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >,  
      boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1> > > > &>' requested here  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
    ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/detail/config.hpp:134:37: note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
# define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
                                    ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/async_result.hpp:365:17: note: in instantiation of function template specialization  
      'boost::asio::async_result<boost::asio::detail::spawn_helper<boost::asio::executor_binder<void (*)(), boost::asio::io_context::strand>, boost::_bi::bind_t<void, boost::_mfi::mf1<void, toys::socks5::SOCKS5Client,  
      boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,  
      boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >,  
      boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1> > > >, void ()>::initiate<boost::asio::detail::initiate_dispatch,  
      boost::asio::detail::spawn_helper<boost::asio::executor_binder<void (*)(), boost::asio::io_context::strand>, boost::_bi::bind_t<void, boost::_mfi::mf1<void, toys::socks5::SOCKS5Client,  
      boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,  
      boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >,  
      boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1> > > > &>' requested here  
    Signature>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
                ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/impl/dispatch.hpp:211:10: note: in instantiation of function template specialization  
      'boost::asio::async_initiate<boost::asio::detail::spawn_helper<boost::asio::executor_binder<void (*)(), boost::asio::io_context::strand>, boost::_bi::bind_t<void, boost::_mfi::mf1<void,  
      toys::socks5::SOCKS5Client, boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,  
      boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >,  
      boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1> > > > &, void (), boost::asio::detail::initiate_dispatch>' requested here  
  return async_initiate<CompletionToken, void()>(  
         ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/impl/spawn.hpp:504:16: note: in instantiation of function template specialization 'boost::asio::spawn<boost::asio::executor_binder<void (*)(),  
      boost::asio::io_context::strand>, boost::_bi::bind_t<void, boost::_mfi::mf1<void, toys::socks5::SOCKS5Client, boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(),  
      boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >, boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1> > > >' requested  
      here  
  boost::asio::spawn(boost::asio::bind_executor(  
               ^  
/Users/dylu/Documents/20210211/tun2socks-w/socks5/socks5_client.cpp:46:18: note: in instantiation of function template specialization 'boost::asio::spawn<boost::_bi::bind_t<void, boost::_mfi::mf1<void,  
      toys::socks5::SOCKS5Client, boost::asio::basic_yield_context<boost::asio::executor_binder<void (*)(), boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>,  
      boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > > > > >,  
      boost::_bi::list2<boost::_bi::value<toys::socks5::SOCKS5Client *>, boost::arg<1> > > >' requested here  
    boost::asio::spawn(this->strand_,  
                 ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/execution/any_executor.hpp:1389:3: note: candidate constructor not viable: no known conversion from 'boost::asio::io_context::strand' to 'std::nullptr_t'  
      (aka 'nullptr_t') for 1st argument  
  any_executor(nullptr_t) BOOST_ASIO_NOEXCEPT  
  ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/execution/any_executor.hpp:1432:3: note: candidate constructor not viable: no known conversion from 'boost::asio::io_context::strand' to 'const  
      boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >' for 1st argument  
  any_executor(const any_executor& other) BOOST_ASIO_NOEXCEPT  
  ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/execution/any_executor.hpp:1459:3: note: candidate constructor not viable: no known conversion from 'boost::asio::io_context::strand' to  
      'boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >' for 1st argument  
  any_executor(any_executor&& other) BOOST_ASIO_NOEXCEPT  
  ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/execution/any_executor.hpp:1396:3: note: candidate template ignored: requirement 'conditional<!is_same<strand, any_executor<context_as_t<execution_context &>,  
      never_t<0>, prefer_only<possibly_t<0> >, prefer_only<tracked_t<0> >, prefer_only<untracked_t<0> >, prefer_only<fork_t<0> >, prefer_only<continuation_t<0> > > >::value && !is_base_of<any_executor_base,  
      strand>::value, is_valid_target_executor<strand, void (context_as_t<execution_context &>, never_t<0>, prefer_only<possibly_t<0> >, prefer_only<tracked_t<0> >, prefer_only<untracked_t<0> >, prefer_only<fork_t<0>  
      >, prefer_only<continuation_t<0> >)>, integral_constant<bool, false> >::is_valid_target_executor<boost::asio::io_context::strand, void (boost::asio::execution::context_as_t<boost::asio::execution_context &>,  
      boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >,  
      boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> >)>::value' was  
      not satisfied [with Executor = boost::asio::io_context::strand]  
  any_executor(Executor ex,  
  ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/execution/any_executor.hpp:1413:3: note: candidate template ignored: could not match 'any_executor<type-parameter-0-0...>' against  
      'boost::asio::io_context::strand'  
  any_executor(any_executor<OtherSupportableProperties...> other,  
  ^  
/Users/dylu/Documents/20210211/boost_1_75_0/boost/asio/execution/any_executor.hpp:1383:3: note: candidate constructor not viable: requires 0 arguments, but 1 was provided  
  any_executor() BOOST_ASIO_NOEXCEPT  
  ^  
1 error generated.  
make[2]: *** [CMakeFiles/tun2socks.dir/socks5/socks5_client.cpp.o] Error 1  
make[1]: *** [CMakeFiles/tun2socks.dir/all] Error 2  
make: *** [all] Error 2  
dyludeMac:build dylu$

---

## Comment 1

> Username: ghost  
> Created at: 2021-05-19 01:40:22 UTC  
> Url: https://github.com/boostorg/asio/issues/375#issuecomment-843680144  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#824](https://github.com/chriskohlhoff/asio/issues/824).
