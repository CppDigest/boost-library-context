# #105 - skynet variants crash on windows [Closed]

> Username: brandon-kohn  
> Created at: 2017-01-10 18:51:30 UTC  
> Updated at: 2017-04-02 05:12:38 UTC  
> Closed at: 2017-04-02 05:12:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/105  

I'm trying to run the skynet benchmarks on windows, and find that they all crash. I build with visual studio 2015 and tag boost-1.63.0.   
  
The crash is an access violation down in context:  
  
>	skynet.exe!boost::intrusive::list_node_traits<void * __ptr64>::set_next(boost::intrusive::list_node<void *> * const & n, boost::intrusive::list_node<void *> * const & next) Line 66	C++  
 	skynet.exe!boost::intrusive::circular_list_algorithms<boost::intrusive::list_node_traits<void * __ptr64> >::unlink(boost::intrusive::list_node<void *> * const & this_node) Line 146	C++  
 	skynet.exe!boost::intrusive::list_impl<boost::intrusive::mhtraits<boost::fibers::context,boost::intrusive::list_member_hook<boost::intrusive::tag<boost::fibers::detail::ready_tag>,boost::intrusive::link_mode<2>,void>,104>,unsigned __int64,0,void>::pop_front_and_dispose<boost::intrusive::detail::null_disposer>(boost::intrusive::detail::null_disposer disposer) Line 349	C++  
 	skynet.exe!boost::fibers::algo::round_robin::pick_next() Line 36	C++  
 	skynet.exe!boost::fibers::scheduler::dispatch() Line 153	C++  
 	skynet.exe!boost::fibers::context::<lambda>(boost::context::execution_context<boost::fibers::detail::data_t *> ctx, boost::fibers::detail::data_t * dp) Line 225	C++  
 	[External Code]	  
 	skynet.exe!make_fcontext() Line 158	Unknown

---

## Comment 1

> Username: DePizzottri  
> Created at: 2017-01-11 02:53:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/105#issuecomment-271764001  

I confirm this fact. But increasing stack_size to 4048*8 leads to a decrease of the frequency of crashes up to nearly 1/10.

---

## Comment 2

> Username: olk  
> Created at: 2017-01-11 07:24:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/105#issuecomment-271797118  

the link list from boost.intrusive stores the predecessor and successor as raw pointers - needs to be replaced by linked list using std::atomic<>.

---

## Comment 3

> Username: brandon-kohn  
> Created at: 2017-01-13 00:53:08 UTC  
> Updated at: 2017-01-13 01:04:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/105#issuecomment-272330714  

Is anything being done to rectify this, or does it need to be pursued in boost devel? What I mean to say, is to ask whether Ion is aware of this issue, or if it needs to be pursued?

---

## Comment 4

> Username: olk  
> Created at: 2017-01-13 06:42:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/105#issuecomment-272373004  

It will be fixed but I've to do it at my free-time and I'm very busy at the moment.

---

## Comment 5

> Username: olk  
> Created at: 2017-04-02 05:12:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/105#issuecomment-290965225  

should be fixed in the current development branch (or migh be in boost-1.64)
