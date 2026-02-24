# #72 - VS2019 / C++ 17, signal::disconnect compile error: syntax error: 'template' [Open]

> Username: johny7  
> Created at: 2023-11-14 12:43:35 UTC  
> Updated at: 2023-11-17 14:40:51 UTC  
> Url: https://github.com/boostorg/signals2/issues/72  

Using custom functor, Signals fail to compile with:  
signal_template.hpp(537,50): error C2059: syntax error: 'template'  
  
Here:  
```  
              // check for wrapped extended slot  
              bound_extended_slot_function_type *fp;  
              fp = (*it)->slot().slot_function().template target<bound_extended_slot_function_type>();  
```  
  
The reason behind is that the signal expects the functor to be the boost::function / std::function, that would contain the target() method.   
  
Minimal example:  
```  
struct MyFunctorThisComparer  
{  
	MyFunctorThisComparer(const void* this_) {}  
	bool operator==(const MyFunctorThisComparer& other) const { return true; }  
};  
  
struct MyFunctor  
{  
	typedef void result_type;  
  
	template<typename F>  
	void operator=(F) {}  
	void operator()() {}  
  
	// disconnect by comparison with custom predicate  
	bool operator==(const MyFunctorThisComparer& other) const { return true; }  
	operator MyFunctorThisComparer() const { return MyFunctorThisComparer { nullptr }; }  
};  
  
void signals_disconnect()  
{  
	boost::signals2::signal<  
		void(),   
		//SignalSupport_::AllCall<typename boost::function_traits<void()>::result_type>,  
		boost::signals2::optional_last_value<typename boost::function_traits<void()>::result_type>,  
		int,  
		std::less<int>,  
		MyFunctor > s;  
  
	s.connect( &signals_disconnect );  
	s.disconnect( MyFunctorThisComparer(nullptr) );  
}  
```  
  
I attempted here to provide the equivalence MyFunctorThisComparer predicate, that would filter out functors. In particular, it would know how to extract 'this' pointer from MyFunctor. However, it could be a more generic predicate.  
  
Boost 1.72.0. However, I have checked the development version - it has the same lines there, so the issue is still present.

---

## Comment 1

> Username: fmhess  
> Created at: 2023-11-17 14:40:50 UTC  
> Url: https://github.com/boostorg/signals2/issues/72#issuecomment-1816549213  

I've never given much thought to allowing for the SlotFunction to be anything other than boost::function or std::function, although I do remember considering dropping the SlotFunction template parameter entirely.  Recent changes to support c++20 means operator== won't even be used in the future (it uses the "contains" method instead).  I guess it might be possible to get rid of the "target" call and just rely on "contains".
