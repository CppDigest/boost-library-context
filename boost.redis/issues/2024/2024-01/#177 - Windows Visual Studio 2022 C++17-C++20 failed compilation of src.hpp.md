# #177 - Windows Visual Studio 2022 C++17/C++20 failed compilation of src.hpp. [Closed]

> Username: RayDavidWhitmer  
> Created at: 2024-01-04 18:38:31 UTC  
> Updated at: 2024-03-10 09:03:24 UTC  
> Closed at: 2024-03-10 09:03:24 UTC  
> Url: https://github.com/boostorg/redis/issues/177  

I am working in recent download of visual studio 2022. I tried the following with permissive option true in C++17 and C++ 20 modes.  
  
This all happens before I have tried to use redis or asio in any way other than including boost/redis/src.hpp. I would be happy with some sort of work-around. Is it a VS error I should report there, or something else?  
  
I downloaded prebuilt 1.84 windows binaries from the linked source on boost.org with inclusion tree, added to the include path and supplied the boost::redis mandatory openssl dependency.  
  
I created a brief .cpp file that includes boost/redis/src.hpp.  
  
Upon compilation, I get the error:  
'boost::redis::basic_connection<boost::redis::connection::executor_type>::basic_connection': no overloaded function could convert all the argument types	C:\Users\rwhitmer\foo\esc\boost\boost\redis\impl\connection.ipp	22  
  
The line in question is:  
  
impl_{ioc.get_executor(), method, max_read_size}  
  
According to VS, ioc.get_executor() returns boost::asio::io_context::executor_type and the constructor requires something different something with a name like, asio::any_*_executor and unconvertable.  
  
I look at the class redis/connection.hpp and asio/any_*_executor to try to figure out how this problem arises. I find the documentation of BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT which seems related to this sort of failure, together with documentation of proposed standard executors, etc. Defining this macro changes the error.  
  
The error with the definition is:  
  
Error	C2182	'executor_': this use of 'void' is not valid		C:\Users\rwhitmer\foo\esc\boost\boost\asio\experimental\impl\parallel_group.hpp	135		  
  
The line in question says:  
  
executor_type executor_;  
  
which seems to rely on the typedef on line 88:  
  
  typedef decay_t<  
      prefer_result_t<  
        associated_executor_t<Handler>,  
        execution::outstanding_work_t::tracked_t  
      >  
    > executor_type;  
  
The additional information given in the compiler output is:  
<details><summary>click to expand</summary><p>  
1>C:\Users\rwhitmer\foo\esc\boost\boost\asio\experimental\impl\parallel_group.hpp(135,17):  
1>the template instantiation context (the oldest one first) is  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\redis\impl\connection.ipp(31,9):  
1>	see reference to function template instantiation 'void boost::redis::basic_connection<boost::redis::connection::executor_type>::async_run<boost::redis::logger,boost::asio::any_completion_handler<void (boost::system::error_code)>>(const boost::redis::config &,Logger,CompletionToken)' being compiled  
1>        with  
1>        [  
1>            Logger=boost::redis::logger,  
1>            CompletionToken=boost::asio::any_completion_handler<void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\redis\connection.hpp(158,20):  
1>	see reference to function template instantiation 'void boost::asio::async_compose<CompletionToken,void(boost::system::error_code),boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::basic_waitable_timer<std::chrono::steady_clock,boost::asio::wait_traits<Clock>,boost::asio::any_io_executor>&>(Implementation &&,boost::asio::any_completion_handler<void (boost::system::error_code)> &,boost::asio::basic_waitable_timer<Clock,boost::asio::wait_traits<Clock>,boost::asio::any_io_executor> &)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::any_completion_handler<void (boost::system::error_code)>,  
1>            Logger=boost::redis::logger,  
1>            Clock=std::chrono::steady_clock,  
1>            Implementation=boost::redis::detail::reconnection_op<this_type,boost::redis::logger>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\compose.hpp(307,10):  
1>	see reference to function template instantiation 'void boost::asio::async_initiate<CompletionToken,Signature,boost::asio::detail::initiate_composed_op<Signature,void (boost::asio::executor)>,Implementation>(Initiation &&,boost::asio::any_completion_handler<void (boost::system::error_code)> &,Implementation &&)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::any_completion_handler<void (boost::system::error_code)>,  
1>            Signature=void (boost::system::error_code),  
1>            Implementation=boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,  
1>            Initiation=boost::asio::detail::initiate_composed_op<void (boost::system::error_code),void (boost::asio::executor)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\async_result.hpp(570,65):  
1>	see reference to function template instantiation 'boost::asio::detail::completion_handler_async_result<CompletionToken,Signature>::return_type boost::asio::detail::completion_handler_async_result<CompletionToken,Signature>::initiate<Initiation,CompletionToken,Implementation>(Initiation &&,RawCompletionToken &&,Implementation &&)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::any_completion_handler<void (boost::system::error_code)>,  
1>            Signature=void (boost::system::error_code),  
1>            Initiation=boost::asio::detail::initiate_composed_op<void (boost::system::error_code),void (boost::asio::executor)>,  
1>            Implementation=boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,  
1>            RawCompletionToken=boost::asio::any_completion_handler<void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\async_result.hpp(273,31):  
1>	see reference to function template instantiation 'void boost::asio::detail::initiate_composed_op<Signature,void (boost::asio::executor)>::operator ()<RawCompletionToken,Implementation>(Handler &&,Impl &&) const' being compiled  
1>        with  
1>        [  
1>            Signature=void (boost::system::error_code),  
1>            RawCompletionToken=boost::asio::any_completion_handler<void (boost::system::error_code)>,  
1>            Implementation=boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,  
1>            Handler=boost::asio::any_completion_handler<void (boost::system::error_code)>,  
1>            Impl=boost::redis::detail::reconnection_op<this_type,boost::redis::logger>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\compose.hpp(165,35):  
1>	see reference to function template instantiation 'void boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>::operator ()<>(void)' being compiled  
1>        with  
1>        [  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code)  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\compose.hpp(92,5):  
1>	see reference to function template instantiation 'void boost::redis::detail::reconnection_op<this_type,Logger>::operator ()<boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>>(Self &,boost::system::error_code)' being compiled  
1>        with  
1>        [  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code),  
1>            Self=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\redis\connection.hpp(38,22):  
1>	see reference to function template instantiation 'void boost::redis::detail::connection_base<boost::asio::executor>::async_run<Logger,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>>(const boost::redis::config &,Logger,CompletionToken)' being compiled  
1>        with  
1>        [  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code),  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\redis\detail\connection_base.hpp(500,21):  
1>	see reference to function template instantiation 'void boost::redis::detail::runner<boost::asio::executor>::async_run<boost::redis::detail::connection_base<boost::asio::executor>,Logger,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>>(Connection &,Logger,CompletionToken)' being compiled  
1>        with  
1>        [  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code),  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\redis\detail\runner.hpp(196,20):  
1>	see reference to function template instantiation 'void boost::asio::async_compose<CompletionToken,void(boost::system::error_code),boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>,Connection&>(Implementation &&,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature> &,Connection &)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            Implementation=boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code)  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\compose.hpp(307,10):  
1>	see reference to function template instantiation 'void boost::asio::async_initiate<CompletionToken,Signature,boost::asio::detail::initiate_composed_op<Signature,void (boost::asio::executor)>,Implementation>(Initiation &&,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature> &,Implementation &&)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Signature=void (boost::system::error_code),  
1>            Implementation=boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,  
1>            Initiation=boost::asio::detail::initiate_composed_op<void (boost::system::error_code),void (boost::asio::executor)>,  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor)  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\async_result.hpp(570,65):  
1>	see reference to function template instantiation 'boost::asio::detail::completion_handler_async_result<CompletionToken,Signature>::return_type boost::asio::detail::completion_handler_async_result<CompletionToken,Signature>::initiate<Initiation,CompletionToken,Implementation>(Initiation &&,RawCompletionToken &&,Implementation &&)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Signature=void (boost::system::error_code),  
1>            Initiation=boost::asio::detail::initiate_composed_op<void (boost::system::error_code),void (boost::asio::executor)>,  
1>            Implementation=boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,  
1>            RawCompletionToken=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\async_result.hpp(273,31):  
1>	see reference to function template instantiation 'void boost::asio::detail::initiate_composed_op<Signature,void (boost::asio::executor)>::operator ()<RawCompletionToken,Implementation>(Handler &&,Impl &&) const' being compiled  
1>        with  
1>        [  
1>            Signature=void (boost::system::error_code),  
1>            RawCompletionToken=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Implementation=boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,  
1>            Handler=boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Impl=boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\compose.hpp(165,35):  
1>	see reference to function template instantiation 'void boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>,Signature>::operator ()<>(void)' being compiled  
1>        with  
1>        [  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code)  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\compose.hpp(92,5):  
1>	see reference to function template instantiation 'void boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::operator ()<boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>,Signature>>(Self &,std::array<size_t,3>,boost::system::error_code,boost::system::error_code,boost::system::error_code,size_t)' being compiled  
1>        with  
1>        [  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code),  
1>            Self=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\redis\detail\runner.hpp(96,11):  
1>	see reference to function template instantiation 'void boost::asio::experimental::parallel_group<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>>::async_wait<boost::asio::experimental::wait_for_all,boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>,Signature>>(CancellationCondition,CompletionToken &&)' being compiled  
1>        with  
1>        [  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code),  
1>            CancellationCondition=boost::asio::experimental::wait_for_all,  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\experimental\parallel_group.hpp(200,25):  
1>	see reference to function template instantiation 'void boost::asio::async_initiate<CompletionToken,void(std::array<size_t,3>,boost::system::error_code,boost::system::error_code,boost::system::error_code),boost::asio::experimental::parallel_group<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>>::initiate_async_wait,boost::asio::experimental::wait_for_all,std::tuple<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>>>(Initiation &&,boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>,Signature> &,boost::asio::experimental::wait_for_all &&,std::tuple<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>> &&)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            Initiation=boost::asio::experimental::parallel_group<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>::()::<lambda_3>>::initiate_async_wait,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code)  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\async_result.hpp(570,65):  
1>	see reference to function template instantiation 'boost::asio::detail::completion_handler_async_result<CompletionToken,void (std::array<size_t,3>,boost::system::error_code,boost::system::error_code,boost::system::error_code)>::return_type boost::asio::detail::completion_handler_async_result<CompletionToken,void (std::array<size_t,3>,boost::system::error_code,boost::system::error_code,boost::system::error_code)>::initiate<Initiation,CompletionToken,boost::asio::experimental::wait_for_all,std::tuple<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>>>(Initiation &&,RawCompletionToken &&,boost::asio::experimental::wait_for_all &&,std::tuple<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>> &&)' being compiled  
1>        with  
1>        [  
1>            CompletionToken=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Initiation=boost::asio::experimental::parallel_group<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>::()::<lambda_3>>::initiate_async_wait,  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            RawCompletionToken=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\async_result.hpp(273,31):  
1>	see reference to function template instantiation 'void boost::asio::experimental::parallel_group<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>>::initiate_async_wait::operator ()<RawCompletionToken,boost::asio::experimental::wait_for_all>(Handler &&,Condition &&,std::tuple<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>> &&) const' being compiled  
1>        with  
1>        [  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            RawCompletionToken=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Handler=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Condition=boost::asio::experimental::wait_for_all  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\experimental\parallel_group.hpp(150,15):  
1>	see reference to function template instantiation 'void boost::asio::experimental::detail::parallel_group_launch<_Ty,boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,Logger>,boost::asio::detail::composed_work<Executors>,boost::asio::any_completion_handler<void (boost::system::error_code)>,Signature>,Signature>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>,0,1,2>(Condition,Handler,std::tuple<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>> &,std::integer_sequence<size_t,0,1,2>)' being compiled  
1>        with  
1>        [  
1>            _Ty=boost::asio::experimental::wait_for_all,  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger,  
1>            Executors=void (boost::asio::executor),  
1>            Signature=void (boost::system::error_code),  
1>            Condition=boost::asio::experimental::wait_for_all,  
1>            Handler=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\experimental\impl\parallel_group.hpp(386,8):  
1>	see reference to class template instantiation 'boost::asio::experimental::detail::parallel_group_state<Condition,Handler,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>>' being compiled  
1>        with  
1>        [  
1>            Condition=boost::asio::experimental::wait_for_all,  
1>            Handler=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger  
1>        ]  
1>	C:\Users\rwhitmer\foo\esc\boost\boost\asio\experimental\impl\parallel_group.hpp(183,54):  
1>	see reference to class template instantiation 'boost::asio::experimental::detail::parallel_group_completion_handler<Handler,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_1>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_2>,boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,Connection,Logger>::()::<lambda_3>>' being compiled  
1>        with  
1>        [  
1>            Handler=boost::asio::detail::composed_op<boost::redis::detail::runner_op<boost::redis::detail::runner<boost::asio::executor>,boost::redis::detail::connection_base<boost::asio::executor>,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::detail::composed_op<boost::redis::detail::reconnection_op<this_type,boost::redis::logger>,boost::asio::detail::composed_work<void (boost::asio::executor)>,boost::asio::any_completion_handler<void (boost::system::error_code)>,void (boost::system::error_code)>,void (boost::system::error_code)>,  
1>            Connection=boost::redis::detail::connection_base<boost::asio::executor>,  
1>            Logger=boost::redis::logger  
1>        ]  
</p></details>

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-01-04 18:53:29 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1877601337  

I am also testing with vs 2022 in the CI and it is passing, see https://github.com/boostorg/redis/blob/112bba722211b16ca4fbda16291329368809fb7e/.github/workflows/ci.yml#L23  
  
Can you provide me with the example you are trying to compile? Regarding `BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT`, there was a user having a problem with it, see https://github.com/boostorg/redis/issues/110  
  
I have also made some changes in the place you are pointing to after the release, they are still on the develop. You can try using it as I described in https://github.com/boostorg/redis/issues/175#issuecomment-1873505988

---

## Comment 2

> Username: RayDavidWhitmer  
> Created at: 2024-01-04 20:49:03 UTC  
> Updated at: 2024-01-04 21:20:13 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1877741407  

Marcelo, the code I was trying to compile was pretty simple:  
  
//#define BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT  
#include "boost/redis/src.hpp"  
  
I hadn't gotten any further (or I ifdef'ed the rest when this part failed).  
  
Possibly relevant command line options are:  
  
/ifcOutput ".\VS17.0\x64Release\" /GS /W4 /Gy /Zc:wchar_t /I"..\..\esc\boost" /I"..\..\esc\openssl\include" /Z7 /Gm- /O2 /Ob1 /Fd".\VS17.0\x64Release\" /Zc:inline /fp:precise /D "NDEBUG" /D "_CONSOLE" /D "__WINNT__" /D "_VC80_UPGRADE=0x0600" /D "_MBCS" /errorReport:prompt /GF /WX- /Zc:forScope /Gd /MT /std:c++20 /Fa".\VS17.0\x64Release\" /EHsc /nologo /Fo".\VS17.0\x64Release\" /diagnostics:column   
  
I am generally using static libraries, as I have been successfully doing for quite some time, but this failure is compilation.  
  
I am pulling down a freshest copy of boost includes to see if that makes a difference. I failed to fully follow your instructions on the pull. Retrying.

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-01-04 21:18:28 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1877773320  

I have merged two PR in the develop branch after Boost. 1.84 was out, one of them changed the connection constructor. Make sure you checkout the develop branch `git submodule foreach --recursive git checkout develop` as I linked you above.  
  
Otherwise, please provide me with the exact details of the vs version you are using and I will check if it is not already being tested in CI and whether github actions supports it.

---

## Comment 4

> Username: RayDavidWhitmer  
> Created at: 2024-01-04 22:01:48 UTC  
> Updated at: 2024-01-05 15:10:21 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1877819249  

I successfully transferred your changes.  
  
No change in the errors.  
  
Line 22 in connection.ipp where the error occurs was changed by your changes, but the first argument and the type of the first argument were not changed, which is what causes the error, only the second and third argument were changed.  
  
Here is the version of my VS:  
  
Microsoft Visual Studio Enterprise 2022  
Version 17.8.3  
VisualStudio.17.Release/17.8.3+34330.188  
Microsoft .NET Framework  
Version 4.8.09037  
<details><summary>click to expand</summary><p>  
Installed Version: Enterprise  
  
Visual C++ 2022   00476-80000-00000-AA536  
Microsoft Visual C++ 2022  
  
ADL Tools Service Provider   1.0  
This package contains services used by Data Lake tools  
  
ASA Service Provider   1.0  
  
ASP.NET and Web Tools   17.8.358.6298  
ASP.NET and Web Tools  
  
Azure App Service Tools v3.0.0   17.8.358.6298  
Azure App Service Tools v3.0.0  
  
Azure Data Lake Tools for Visual Studio   2.6.5000.0  
Microsoft Azure Data Lake Tools for Visual Studio  
  
Azure Functions and Web Jobs Tools   17.8.358.6298  
Azure Functions and Web Jobs Tools  
  
Azure Stream Analytics Tools for Visual Studio   2.6.5000.0  
Microsoft Azure Stream Analytics Tools for Visual Studio  
  
C# Tools   4.8.0-7.23572.1+7b75981cf3bd520b86ec4ed00ec156c8bc48e4eb  
C# components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
Common Azure Tools   1.10  
Provides common services for use by Azure Mobile Services and Microsoft Azure Tools.  
  
Cookiecutter   17.0.23262.1  
Provides tools for finding, instantiating and customizing templates in cookiecutter format.  
  
Microsoft Azure Hive Query Language Service   2.6.5000.0  
Language service for Hive query  
  
Microsoft Azure Stream Analytics Language Service   2.6.5000.0  
Language service for Azure Stream Analytics  
  
Microsoft Azure Tools for Visual Studio   2.9  
Support for Azure Cloud Services projects  
  
Microsoft JVM Debugger   1.0  
Provides support for connecting the Visual Studio debugger to JDWP compatible Java Virtual Machines  
  
Node.js Tools   1.5.50908.1 Commit Hash:c294679b821c4b8f7f9c6db3827b7655db80fc28  
Adds support for developing and debugging Node.js apps in Visual Studio  
  
NuGet Package Manager   6.8.0  
NuGet Package Manager in Visual Studio. For more information about NuGet, visit https://docs.nuget.org/  
  
Python - Profiling support   17.0.23262.1  
Profiling support for Python projects.  
  
Python with Pylance   17.0.23262.1  
Provides IntelliSense, projects, templates, debugging, interactive windows, and other support for Python developers.  
  
Razor (ASP.NET Core)   17.8.3.2358002+8c7fb27bf8e8d4f9ff8080b624b35bca5e812e97  
Provides languages services for ASP.NET Core Razor.  
  
SQL Server Data Tools   17.8.119.0  
Microsoft SQL Server Data Tools  
  
Test Adapter for Boost.Test   1.0  
Enables Visual Studio's testing tools with unit tests written for Boost.Test.  The use terms and Third Party Notices are available in the extension installation directory.  
  
Test Adapter for Google Test   1.0  
Enables Visual Studio's testing tools with unit tests written for Google Test.  The use terms and Third Party Notices are available in the extension installation directory.  
  
ToolWindowHostedEditor   1.0  
Hosting json editor into a tool window  
  
TypeScript Tools   17.0.20920.2001  
TypeScript Tools for Microsoft Visual Studio  
  
Visual Basic Tools   4.8.0-7.23572.1+7b75981cf3bd520b86ec4ed00ec156c8bc48e4eb  
Visual Basic components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
Visual C++ for Cross Platform Mobile Development (Android)   15.0.34205.153  
Visual C++ for Cross Platform Mobile Development (Android)  
  
Visual F# Tools   17.8.0-beta.23475.2+10f956e631a1efc0f7f5e49c626c494cd32b1f50  
Microsoft Visual F# Tools  
  
Visual Studio IntelliCode   2.2  
AI-assisted development for Visual Studio.  
  
</p></details>

---

## Comment 5

> Username: RayDavidWhitmer  
> Created at: 2024-01-04 22:07:02 UTC  
> Updated at: 2024-01-04 22:10:20 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1877824768  

I might be able to run the CI tests on my version of VS, but I first need to study the tests so I understand how and what it means if they pass or fail.  
  
I can also try compiling src.hpp on VS version 19 to see if that works.

---

## Comment 6

> Username: mzimbres  
> Created at: 2024-01-04 22:23:53 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1877841055  

> I might be able to run the CI tests on my version of VS, but I first need to study the tests so I understand how and what it means if they pass or fail.  
  
IIUC, we must first understand why you cannot build, running the tests are not necessary.

---

## Comment 7

> Username: RayDavidWhitmer  
> Created at: 2024-01-04 23:59:34 UTC  
> Updated at: 2024-01-05 15:13:44 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1877924432  

I thought I had tested C++17 mode before, but I just retested.  
  
Now when I flip the standard to C++17 instead of C++20, it compiles src.hpp . I flip back to C++20 and it stops compiling with the stated error. (That would mean no co routines).  
  
[I added clickable expansions to some of the lengthy details above to make it more readable.]  
  
Let me know if there is more required from me in reproducing this issue.

---

## Comment 8

> Username: mzimbres  
> Created at: 2024-01-06 19:52:48 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1879801374  

Hi, are you using cmake? Can you copy here the exact command you are using to generate the cmake files? I had a look in the CI and the tools you are using a covered, [for example](https://github.com/boostorg/redis/actions/runs/7407828648/job/20154784269)  
  
```  
** Visual Studio 2022 Developer Command Prompt v17.8.3  
  
###  
### Using 'vc143' toolset.  
###  
```  
  
I don't see what can be causing your issue.

---

## Comment 9

> Username: RayDavidWhitmer  
> Created at: 2024-01-08 20:30:41 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1881775616  

I am not using cmake. I downloaded the prebuilt Windows binaries, and added the boost include path to the include path of the project in question, at which point it fails if I have the language set to C++20, but not if I have the language set to C++17. It never gets close to linking when it fails. It fails during compilation due including src.hpp, the only thing being compiled.

---

## Comment 10

> Username: RayDavidWhitmer  
> Created at: 2024-01-08 22:03:27 UTC  
> Updated at: 2024-01-08 22:23:30 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1881892899  

[boostissue.zip](https://github.com/boostorg/redis/files/13866556/boostissue.zip)  
If you unzip this file it creates the directory boostissue which has boostredisfailure that contains Visual Studio 2022 solution/project and a single source file that #include's "boost/redis/src.hpp".  
  
If you check out the latest version of boost into a boost directory in the boostissue directory, the rest of the dependencies are satisfied. I added the individual paths under libs to the include path until they were all satisfied, as well as the include directory of openssl.  
  
If you compile this, as-is, you get the compiler error I reported above and one other I never saw before.  
  
If you change the compiler to c++17 instead of c++20, then you see link errors, which is success because it means that the compilations succeeded and I didn't provide libraries to link against.  
  
The error also seems to go away if you put it in non-permissive mode, which is an impossibility for large projects without many thousands of difficult fixes. I guess I will look at the option of compiling different files with different switches.

---

## Comment 11

> Username: RayDavidWhitmer  
> Created at: 2024-01-09 18:32:53 UTC  
> Updated at: 2024-01-09 23:39:56 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1883578599  

Thanks.  
  
I suppose this should be an issue for Microsoft, why are (incorrect?) errors raised when setting permissive mode, which should create fewer errors, not more. Unfortunately our experience with Microsoft Support is that it is generally not usable.  
  
Now that I have provided the simple reproduction, I can understand if no one outside Microsoft chooses to do anything about this compilation failure. At the moment I have the work-around that I can set the one or multiple files in the project that call redis into strict/non-permissive mode which is good enough for now until we can migrate to a better platform.

---

## Comment 12

> Username: RayDavidWhitmer  
> Created at: 2024-01-26 21:54:20 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1912745589  

FWIW There are a lot more things in C++20 and boost that fail in MSVC permissive mode. Permissive mode is deeply broken, but that is why there is strict mode and it is the default when using C++20. I was just starting with C++20 in MSVC so I didn't know. The errors make no sense to me but I have yet to find failures in strict mode.

---

## Comment 13

> Username: mzimbres  
> Created at: 2024-02-18 21:09:02 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1951445652  

Can we close this issue? It is not related to Boost.Redis AFAICS.

---

## Comment 14

> Username: RayWhitmer  
> Created at: 2024-03-06 16:20:14 UTC  
> Url: https://github.com/boostorg/redis/issues/177#issuecomment-1981251213  

Yes, please close it. The problem was with Visual Studio running in non-strict mode, which is crazy to me that it wouldn't compile, but that is Microsoft's problem I believe.
