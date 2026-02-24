# #333 - future<shared_future<T>>::unwrap does not exist [Open]

> Username: thisismiller  
> Created at: 2020-12-10 02:31:37 UTC  
> Updated at: 2020-12-10 06:02:12 UTC  
> Url: https://github.com/boostorg/thread/issues/333  

The docs say:  
  
```  
Member function unwrap() EXTENSION  
template <typename R2>  
 future<R2>  future< future<R2>>::unwrap();  // EXTENSION  
template <typename R2>  
 boost::shared_future<R2>  future< boost::shared_future<R2>>::unwrap();  // EXTENSION  
```  
  
[thread.hpp:1887](https://github.com/boostorg/thread/blob/409c98f8b745e72bc326e93bfaf8a353d94a69b0/include/boost/thread/future.hpp#L1887) appears to define the future<future\<R2\>>::unwrap, but it doesn't appear that there's a specialization for future<shared_future\<R2\>> to provide the documented `unwrap` method.
