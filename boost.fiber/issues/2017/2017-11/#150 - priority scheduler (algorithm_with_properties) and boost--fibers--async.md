# #150 - priority scheduler (algorithm_with_properties) and boost::fibers::async [Closed]

> Username: niekbouman  
> Created at: 2017-11-04 17:18:08 UTC  
> Updated at: 2022-09-18 14:41:03 UTC  
> Closed at: 2022-09-18 14:41:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/150  

Dear @olk ,  
  
I am a using Goodspeed's implementation of a priority scheduler.  
Now, I'd like to combine this with the `boost::fibers::async` function (which wraps a callable into a packaged_task, runs the task on a fiber and returns a `boost::fibers::future` to the async result of the callable)  
  
I copied the implementation of boost::fibers::async into the priority.cpp example, and added some lines of code (taken from Goodspeed's `launch` function in priority.cpp) that set the priority of the fiber:  
```cpp      
    priority_props & props( fiber.properties< priority_props >() );  
    props.name = name;  
    props.set_priority( priority);  
```  
(See the full implementation of the modified async function, called `myasync` below.)  
   
Now, if I run `async` with the `launch::post` policy, everything works fine. However, if I run async with the `launch::dispatch` I get the error   
`  
Assertion failed: ((props)&&("fiber::properties not set")), function properties, file /usr/local/include/boost/fiber/fiber.hpp, line 137.  
`  
  
I know that this problem is caused by the fact that the properties object is not yet constructed.   
My question is: What is the proper way to enforce this construction, without using a yield statement (that 'solution' is applied in the `properties()` function in `operations.hpp`)?  
  
  
Full code example in:  
[priority.zip](https://github.com/boostorg/fiber/files/1443405/priority.zip)  
  
best,  
Niek  
  
This is the modified `boost::fibers::async` implementation:  
```cpp  
template< typename Policy, typename Fn, typename ... Args >  
boost::fibers::future<  
    typename std::result_of<  
        typename std::enable_if<  
            boost::fibers::detail::is_launch_policy< Policy >::value,  
            typename std::decay< Fn >::type  
        >::type( typename std::decay< Args >::type ...)  
    >::type  
>  
myasync( Policy policy, Fn && fn, Args ... args) {  
    typedef typename std::result_of<  
        typename std::decay< Fn >::type( typename std::decay< Args >::type ... )  
    >::type     result_type;  
  
    boost::fibers::packaged_task< result_type( typename std::decay< Args >::type ... ) > pt{  
        std::forward< Fn >( fn) };  
    boost::fibers::future< result_type > f{ pt.get_future() };  
    auto fi = boost::fibers::fiber{ policy, std::move( pt), std::forward< Args >( args) ... };   
    //.detach();  
  
    priority_props & props( fi.properties< priority_props >() );  
    props.name = "test";  
    props.set_priority( 10);  
    fi.detach();  
  
    return f;  
}  
```
