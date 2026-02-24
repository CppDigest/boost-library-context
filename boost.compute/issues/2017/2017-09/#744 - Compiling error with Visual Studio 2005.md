# #744 - Compiling error with Visual Studio 2005 [Closed]

> Username: dacmot  
> Created at: 2017-09-21 02:29:06 UTC  
> Updated at: 2017-09-22 21:27:25 UTC  
> Closed at: 2017-09-22 21:27:25 UTC  
> Url: https://github.com/boostorg/compute/issues/744  

Yes I know, Visual Studio 2005 is ancient, but it's what I have to deal with. I'm using boost 1.63.  
```  
3>C:\Program Files (x86)\Microsoft Visual Studio 8\VC\include\vector(1187) : error C2668: 'boost::compute::fill' : ambiguous call to overloaded function  
3>        boost/compute/algorithm/fill.hpp(276): could be 'void boost::compute::fill<boost::compute::device*,_Ty>(BufferIterator,BufferIterator,const T &,boost::compute::command_queue &)' [found using argument-dependent lookup]  
3>        with  
3>        [  
3>            _Ty=boost::compute::device,  
3>            BufferIterator=boost::compute::device *,  
3>            T=boost::compute::device  
3>        ]  
3>        C:\Program Files (x86)\Microsoft Visual Studio 8\VC\include\xutility(2975): or 'void std::fill<boost::compute::device*,_Ty>(_FwdIt,_FwdIt,const _Ty &)'  
3>        with  
3>        [  
3>            _Ty=boost::compute::device,  
3>            _FwdIt=boost::compute::device *  
3>        ]  
3>        while trying to match the argument list '(boost::compute::device *, boost::compute::device *, boost::compute::device)'  
3>        C:\Program Files (x86)\Microsoft Visual Studio 8\VC\include\vector(1117) : while compiling class template member function 'void std::vector<_Ty>::_Insert_n(std::_Vector_iterator<_Ty,_Alloc>,__w64 unsigned int,const _Ty &)'  
3>        with  
3>        [  
3>            _Ty=boost::compute::device,  
3>            _Alloc=std::allocator<boost::compute::device>  
3>        ]  
3>        boost/compute/device.hpp(331) : see reference to class template instantiation 'std::vector<_Ty>' being compiled  
3>        with  
3>        [  
3>            _Ty=boost::compute::device  
3>        ]  
```  
Looking at the vector header file, there's a call to fill() without a namespace (bad Microsoft). I'm not sure what can be done to fix this, if anything at all can be done. I figure maybe someone else with more insight into the framework may have an idea.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-09-21 09:18:52 UTC  
> Url: https://github.com/boostorg/compute/issues/744#issuecomment-331100877  

Try passing `command_queue` to the `boost::compite::fill`, so you have 4 arguments.

---

## Comment 2

> Username: dacmot  
> Created at: 2017-09-21 12:45:49 UTC  
> Url: https://github.com/boostorg/compute/issues/744#issuecomment-331145602  

No, the call to fill() that's problematic isn't in my code. It's in Microsoft's std::vector implementation. That's why I say there's probably nothing that can be done. It stems from the return values std::vector<device> (copy-construction) in device.hpp. Line 331 in the error above is partition(). The same error appears for partition_equally(), partition_by_counts() and partition_by_affinity_domain().

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-09-21 13:01:33 UTC  
> Url: https://github.com/boostorg/compute/issues/744#issuecomment-331149266  

Ah, ok, so in Microsoft's `std::vector` implementation they intended to call `std::fill` but they call `fill()`. That means when we call `std::vector<int> x(1)` inside `boost::compute::` namespace the compiler finds two function templates that matches that. Possible workarounds: (I don't have VS2005, so I may be wrong).  
  
1. Edit `boost::compute::fill` algorithm so there is no default value for `command_queue &queue`, that means different number of arguments.  
2. Edit functions in  `boost::compute::` namespace, so they never call `std::vector<device> devices(count)` (just an example), because this constructor triggers `fill()`. You can replace that with:  
```  
std::vector<device> devices;  
devices.reserve(count);  
```  
In that code `fill()` should not be called, since none of those functions has to fill vector with any data.

---

## Comment 4

> Username: dacmot  
> Created at: 2017-09-22 19:08:15 UTC  
> Url: https://github.com/boostorg/compute/issues/744#issuecomment-331534976  

I'm not sure how to go about (2). The problematic vectors are the return values, so we never actually resize them. They are resized automatically by the copy constructor.  
  
As for (1), that is possible but I'd rather not have a custom version of the library that my co-workers will need to share/be aware of and that will break every time we decide to upgrade.

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-09-22 19:39:52 UTC  
> Url: https://github.com/boostorg/compute/issues/744#issuecomment-331541772  

> I'm not sure how to go about (2). The problematic vectors are the return values, so we never actually resize them. They are resized automatically by the copy constructor.  
  
Oh, I thought it's the `std::vector` that is used inside function and returned is causing this problem. If it's the copy constructor (however, it seems inefficient for copy-ctor to use `fill()`), then I don't have a solution to that problem.  
  
>As for (1), that is possible but I'd rather not have a custom version of the library that my co-workers will need to share/be aware of and that will break every time we decide to upgrade.  
  
That's understandable. Well, in that case unless there's a painless solution you can propose, then we probably won't add a workaround for that VS2005 bug to Boost.Compute.  
  
> Line 331 in the error above is partition(). The same error appears for partition_equally(), partition_by_counts() and partition_by_affinity_domain().  
  
Only in those functions? I mean, there are other functions in Boost.Compute that returns `std::vector` object.

---

## Comment 6

> Username: dacmot  
> Created at: 2017-09-22 21:26:09 UTC  
> Url: https://github.com/boostorg/compute/issues/744#issuecomment-331564441  

Yeah only those functions. Unless visual studio gives up, maybe it's because my code doesn't compile the other ones in.
