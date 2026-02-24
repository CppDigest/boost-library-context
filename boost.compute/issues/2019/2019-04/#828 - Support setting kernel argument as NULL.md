# #828 - Support setting kernel argument as NULL [Closed]

> Username: rosenrodt  
> Created at: 2019-04-02 10:38:12 UTC  
> Updated at: 2019-04-05 02:17:26 UTC  
> Closed at: 2019-04-05 02:17:26 UTC  
> Url: https://github.com/boostorg/compute/issues/828  

As per Khronos OpenCL spec:  
> If the argument is declared to be a pointer of a built-in scalar or vector type, or a user defined structure type in the global or constant address space, the memory object specified as argument value must be a buffer object **(or NULL)**.  
  
I tried to set kernel argument like this   
```c++  
some_kernel.set_arg(0, NULL);  
```  
but get error `invalid argument size` instead.  
  
Because OpenCL explicitly allows NULL as input, I went on to see why and now maybe I've identified the problem:  
  
```c++  
// set_kernel_arg implementation for built-in types (including NULL)  
template<class T>  
struct set_kernel_arg  
{  
    typename boost::enable_if<is_fundamental<T> >::type  
    operator()(kernel &kernel_, size_t index, const T &value)  
    {  
        kernel_.set_arg(index, sizeof(T), &value);  
//                                        ^^^^^^  
//                     now we are getting the address of NULL   
//                     certainly CL API won't allow an arbitrary address  
    }  
};  
```  
So Boost Compute needs somehow to identify the NULL, maybe via a special type inside `boost::compute`, and set the value NULL directly to the CL API instead of its address.

---

## Comment 1

> Username: jszuppe  
> Created at: 2019-04-02 11:36:32 UTC  
> Updated at: 2019-04-02 11:41:15 UTC  
> Url: https://github.com/boostorg/compute/issues/828#issuecomment-478957298  

I don't think it's a valid option. I can undestand why someone would like to pass a null pointer when the argument isn't requied or won't be used in OpenCL C API, but in Boost.Compute's API you would have to pass an uninitialized buffer object.  
  
Also I don't see good solution for supporting that (using `NULL` is ` void set_arg(size_t index, const T &value)`, because `NULL` does not have its own type. We could do that for C++11 only and `std::nullptr_t`.  
  
User can always use `void set_arg(size_t index, size_t size, const void *value)` which will work correctly with `NULL`.

---

## Comment 2

> Username: rosenrodt  
> Created at: 2019-04-02 15:44:52 UTC  
> Url: https://github.com/boostorg/compute/issues/828#issuecomment-479059827  

> User can always use void set_arg(size_t index, size_t size, const void *value) which will work correctly with NULL.  
  
I wasn't aware of that. That should solve my issue, but felt using something like `std::nullptr_t` or `compute::null_t` would be neater. This way it also plays nice with the variadic `set_args(T&&... args)`

---

## Comment 3

> Username: jszuppe  
> Created at: 2019-04-02 16:20:35 UTC  
> Url: https://github.com/boostorg/compute/issues/828#issuecomment-479077028  

We can add overload for `std::nullptr_t`, which will fix it for C++11 and newer, so it should have in variadic version.
