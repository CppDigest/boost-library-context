# #150 - SIGSEGV on scheduler::compensating_work_started() [Closed]

> Username: cotti  
> Created at: 2018-09-26 18:03:18 UTC  
> Updated at: 2020-12-30 00:53:34 UTC  
> Closed at: 2020-12-30 00:53:33 UTC  
> Url: https://github.com/boostorg/asio/issues/150  

We have encountered multiple times what appears to be the same issue as described in https://svn.boost.org/trac10/ticket/13562 on a server application that makes heavy use of asio to send HTTP requests and read their responses.  
  
Our usage is fairly standard: upon getting a connection, we call boost::asio::async_write() binding the next method, onWrite(), and inside it we call boost::asio::async_read_until(), binding onRead() which performs the next step of reading the response to our request, and so on. Our Async HTTP threads are quite short-lived: on our logs their whole life-cycle takes less than a second. We find the threads take a certain amount of time without activity before they are hit with a SIGSEGV - it can vary between a few seconds up to more than a minute, and it seems to happen between onWrite() and onRead().  
  
We were using boost 1.66.0 up until a couple of weeks ago. After this issue occurred a few times, we tried an upgrade to boost 1.68.0, but if anything the frequency this issue is ocurring has increased to almost daily.  
  
```  
        Received signal: 11 - SIGSEGV. Segmentation violation  
        Thread: A-Http-4816 - 140326043744000  
        Stack trace:  
          /server: SignalHandler::getSignalInformation(int)()+0x191  
          /server() [0x144a973]  
          /lib64/libc.so.6: ()+0x35250  
          /server: boost::asio::detail::scheduler::compensating_work_started()()+0x20  
          /server: boost::asio::detail::epoll_reactor::perform_io_cleanup_on_block_exit::~perform_io_cleanup_on_block_exit()()+0x63  
          /server: boost::asio::detail::epoll_reactor::descriptor_state::perform_io(unsigned int)()+0x16d  
          /server: boost::asio::detail::epoll_reactor::descriptor_state::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)()+0x3f  
          /server: boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)()+0x32  
          /server: boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&)()+0x1a8  
          /server: boost::asio::detail::scheduler::run(boost::system::error_code&)()+0x10e  
          /server: boost::asio::io_context::run()()+0x2f  
          /server: boost::_mfi::mf0<unsigned long, boost::asio::io_context>::operator()(boost::asio::io_context*) const()+0x65  
          /server: unsigned long boost::_bi::list1<boost::_bi::value<boost::asio::io_context*> >::operator()<unsigned long, boost::_mfi::mf0<unsigned long, boost::asio::io_context>, boost::_bi::list0>(boost::_bi::type<unsigned long>, boost::_mfi::mf0<unsigned long, boost::asio::io_context>&, boost::_bi::list0&, long)()+0x4b  
          /server: boost::_bi::bind_t<unsigned long, boost::_mfi::mf0<unsigned long, boost::asio::io_context>, boost::_bi::list1<boost::_bi::value<boost::asio::io_context*> > >::operator()()()+0x39  
          /server: unsigned long std::_Bind_simple<boost::_bi::bind_t<unsigned long, boost::_mfi::mf0<unsigned long, boost::asio::io_context>, boost::_bi::list1<boost::_bi::value<boost::asio::io_context*> > > ()>::_M_invoke<>(std::_Index_tuple<>)()+0x28  
          /server: std::_Bind_simple<boost::_bi::bind_t<unsigned long, boost::_mfi::mf0<unsigned long, boost::asio::io_context>, boost::_bi::list1<boost::_bi::value<boost::asio::io_context*> > > ()>::operator()()()+0x1b  
          /server: std::thread::_Impl<std::_Bind_simple<boost::_bi::bind_t<unsigned long, boost::_mfi::mf0<unsigned long, boost::asio::io_context>, boost::_bi::list1<boost::_bi::value<boost::asio::io_context*> > > ()> >::_M_run()()+0x1c  
          /lib64/libstdc++.so.6: ()+0xb5230  
          /lib64/libpthread.so.0: ()+0x7dc5  
          /lib64/libc.so.6: clone()+0x6d  
```

---

## Comment 1

> Username: markand  
> Created at: 2018-10-31 12:27:49 UTC  
> Updated at: 2018-10-31 12:33:19 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-434668250  

Hi, I'm affected by this bug as well.  
  
Building my application with `BOOST_ASIO_DISABLE_EPOLL` works fine, otherwise, I get random crash exactly as @cotti.  
  
It happens on Arch Linux, boost 1.68, linux 4.18.

---

## Comment 2

> Username: oscarfv  
> Created at: 2019-03-25 19:42:09 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-476348489  

On an internal project I can reproduce the problem always. Defining `BOOST_ASIO_DISABLE_EPOLL` does not fix the crash here.  
  
In my code, the calls to `asio` are inside wrapper functions compiled in a shared library. If I move those calls to the main executable, there is no crash.

---

## Comment 3

> Username: joshedwards22  
> Created at: 2020-03-02 17:00:21 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-593505662  

Any update on this? I have encountered this when sharing an io_context with runtime loaded shared libraries on linux. It appears that the "thread_call_stack::contains(this)" is depending on a static variable that does not exist across the module boundary. This "compensating_work_started" seems to be called at random times and for what reason I cannot figure out.

---

## Comment 4

> Username: djarek  
> Created at: 2020-03-02 21:41:28 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-593637542  

@joshedwards22   
Exposing ASIO classes across ABI boundaries is a bad idea - ASIO doesn't make any ABI stability guarantees AFAIK.

---

## Comment 5

> Username: lverbe  
> Created at: 2020-03-12 22:15:24 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-598456319  

This patch fixes it for Boost 1.72.0.  
  
```  
--- ./boost/asio/detail/impl/scheduler.ipp.orig	2020-03-12 11:00:06.823085227 -0600  
+++ ./boost/asio/detail/impl/scheduler.ipp	2020-03-12 11:01:30.898891690 -0600  
@@ -317,8 +317,8 @@ void scheduler::restart()  
   
 void scheduler::compensating_work_started()  
 {  
-  thread_info_base* this_thread = thread_call_stack::contains(this);  
-  ++static_cast<thread_info*>(this_thread)->private_outstanding_work;  
+  if (thread_info_base* this_thread = thread_call_stack::contains(this))  
+    ++static_cast<thread_info*>(this_thread)->private_outstanding_work;  
 }  
   
 void scheduler::post_immediate_completion(  
```

---

## Comment 6

> Username: dillaman  
> Created at: 2020-11-09 13:35:46 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-724017216  

I am also encountering this issue when trying to use `boost::asio` from within shared libraries. In the Ceph project, we are trying to incorporate `boost::asio` in our client-side libraries `librados` and `librbd`, but that results in each shared library's bss section getting its own `boost::asio::detail::call_stack<boost::asio::detail::thread_context, boost::asio::detail::thread_info_base>::top_` and therefore we randomly hit this crash.

---

## Comment 7

> Username: rpopescu  
> Created at: 2020-12-18 13:47:44 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-748093122  

> This patch fixes it for Boost 1.72.0.  
>   
> ```  
> --- ./boost/asio/detail/impl/scheduler.ipp.orig	2020-03-12 11:00:06.823085227 -0600  
> +++ ./boost/asio/detail/impl/scheduler.ipp	2020-03-12 11:01:30.898891690 -0600  
> @@ -317,8 +317,8 @@ void scheduler::restart()  
>    
>  void scheduler::compensating_work_started()  
>  {  
> -  thread_info_base* this_thread = thread_call_stack::contains(this);  
> -  ++static_cast<thread_info*>(this_thread)->private_outstanding_work;  
> +  if (thread_info_base* this_thread = thread_call_stack::contains(this))  
> +    ++static_cast<thread_info*>(this_thread)->private_outstanding_work;  
>  }  
>    
>  void scheduler::post_immediate_completion(  
> ```  
  
I'm really surprised to see this patch not being applied; is there a reason for this?

---

## Comment 8

> Username: oscarfv  
> Created at: 2020-12-18 14:30:46 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-748114194  

@lverbe , @rpopescu : maybe @chriskohlhoff does not monitor this issue tracker.

---

## Comment 9

> Username: rpopescu  
> Created at: 2020-12-18 16:33:21 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-748191916  

@oscarfv do you know what he does monitor? the trac ticket is 3 years old it seems: https://svn.boost.org/trac10/ticket/13562  
thanks.

---

## Comment 10

> Username: oscarfv  
> Created at: 2020-12-18 17:09:36 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-748210111  

@rpopescu : no idea. It seems that he keeps working on https://think-async.com/Asio/ and its Boost incarnation, but I see no way of contacting him on that webpage. Let's wait a bit, maybe he notices the mention on my prior message.

---

## Comment 11

> Username: ghost  
> Created at: 2020-12-30 00:53:32 UTC  
> Url: https://github.com/boostorg/asio/issues/150#issuecomment-752289972  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#642](https://github.com/chriskohlhoff/asio/issues/642).
