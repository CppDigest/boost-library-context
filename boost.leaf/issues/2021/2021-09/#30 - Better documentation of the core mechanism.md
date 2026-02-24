# #30 - Better documentation of the core mechanism [Closed]

> Username: Xeverous  
> Created at: 2021-09-30 20:21:56 UTC  
> Updated at: 2021-10-04 19:40:53 UTC  
> Closed at: 2021-10-04 16:42:25 UTC  
> Url: https://github.com/boostorg/leaf/issues/30  

It is possible to get more information how leaf is implemented? I'm particulary interested in the `on_error` and similar mechanisms which add information about the error context without affecting error-neutral and error-producing function implementations. Somehow leaf knows whether that extra information is useful based on the handler, but the handler can be defined in a separate translation unit.  
  
I would like more information about the internal mechanism:  
  
- How exactly the implementation knows which info is useful and how does it preserve full type information?  
- Does the library use compiler-specific features?  
- Does the library use RTTI? There is a lot of info regarding exceptions but after reading whole documentation I haven't found a single mention of RTTI.

---

## Comment 1

> Username: zajo  
> Created at: 2021-10-01 02:19:09 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-931842545  

Right, this isn't explicitly explained. The way it works is, when you give LEAF a list of error handlers, it parses their argument types, and (internally) instantiates the `leaf::context` class template with these types. For example if you do:  
  
```  
leaf::try_catch(  
    []  
    {  
        ....  
    },  
  
    []( my_error_type x )  
    {  
        ....  
    },  
  
    []( your_error_type x, int y )  
    {  
        ....  
    } );  
```  
  
then LEAF will create an object of type `context<my_error_type, your_error_type, int>`, local to the `leaf::try_catch` function. This object contains a `std::tuple` of the requested types. As explained in https://boostorg.github.io/leaf/#context, when the context is activated, thread-local pointers of the relevant types (in this case `my_error_type`, `your_error_type`, `int`) are pointed to the internal `std::tuple`.  
  
How do `leaf::on_error` and `leaf::new_error`  "know" if a type is needed or not? If the corresponding thread-local pointer is null, they know that nobody cares about that type, because no active `context` in the calling thread uses it, because no error handler takes it as an argument.  
  
The library does not use compiler-specific features. If RTTI is enabled, LEAF uses `dynamic_cast` in the exception handling API. Do note that `try_catch` catches all exceptions, and will match error handlers even if they don't handle exception types.

---

## Comment 2

> Username: Xeverous  
> Created at: 2021-10-03 15:29:04 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-932973689  

> If the corresponding thread-local pointer is null  
  
How that pointer is determined to be null? How is the pointer even visible? If it is not a void pointer there must be some mechanism to create one, but how a error-neutral function implementation know that such variable exists? Is the solution based on visibility of symbols? Can you make an MCVE (without including leaf)?

---

## Comment 3

> Username: zajo  
> Created at: 2021-10-03 20:34:57 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-933020998  

Using the terminology from the LEAF implementation, a `context<E...>` contains `std::tuple<leaf_detail::slot<E>...>`. Therefore the thread-local pointers are of type `leaf_detail::slot<T>`, defined as:  
  
```  
template <class E>  
struct tl_slot_ptr  
{  
    static thread_local slot<E> * p;  
};  
  
template <class E>  
thread_local slot<E> * tl_slot_ptr<E>::p;  
```  
  
The `on_error` machinery simply uses:  
  
```  
slot<E> * e = tl_slot_ptr<E>::p;  
```  
  
Note that objects with `thread_local` duration are zero-initialized, so unless you activate a `context` that contains a type `E`, the corresponding `tl_slot_ptr<E>::p` will be null, which would mean that the calling thread does not currently use `E` to handle errors, and therefore LEAF is free to drop an `E` object if it is ever passed to it.  
  
This enables libraries to pass to LEAF any and all error information that is relevant to a failure, yet user code is in control of what error types are actually needed; LEAF does not waste cycles transporting error objects that are not useful at the time.

---

## Comment 4

> Username: Xeverous  
> Created at: 2021-10-03 21:54:20 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-933031571  

Nice. So it's not a symbol-based solution but an initialization-based one. The thread local pointer exist in all cases, it just isn't set up.  
  
> LEAF does not waste cycles transporting error objects that are not useful at the time.  
  
What's the overhead of such transport vs the overhead of accessing a global thread-local object?

---

## Comment 5

> Username: zajo  
> Created at: 2021-10-04 02:08:23 UTC  
> Updated at: 2021-10-04 02:09:01 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-933088401  

This discusses overhead and contains a few godbolt shares if you're interested in details: https://github.com/boostorg/leaf/blob/develop/benchmark/benchmark.md#show-me-the-code

---

## Comment 6

> Username: Xeverous  
> Created at: 2021-10-04 16:42:25 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-933661832  

Thanks for the info, I can only add that IMO the thread-local-pointer-initialization mechanism should be mentioned in the docs. I would consider myself very knowledgeable on C++ but reading leaf docs didn't give me sufficiently satisfying explanation on how it's done and it wasn't a good feeling. On the other hand lots of non-savvy boost users understand it as "template magic" and don't bother looking how things are made. Maybe it's just my personal feeling that I like to know what I'm using and so far most libraries were more than happy to explain their primary inventions.

---

## Comment 7

> Username: zajo  
> Created at: 2021-10-04 18:30:41 UTC  
> Updated at: 2021-10-04 18:33:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-933745146  

Are you saying that the information in https://boostorg.github.io/leaf/#context is insufficient, or is it that you think that these implementation details need to be presented more prominently? Do you have any other input on the documentation, especially the introduction?

---

## Comment 8

> Username: Xeverous  
> Created at: 2021-10-04 19:18:44 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-933781009  

Just more on how it works from inside. I get the API. I easily get the diagrams about (not) transporting the error object through return values up the stack but the thing I did not get was how the error-neutral functions somehow know that a certain global pointer is available. I did not know what exactly allows error-neutral/producing functions to communicate with the context without knowing where it exists.

---

## Comment 9

> Username: zajo  
> Created at: 2021-10-04 19:40:53 UTC  
> Url: https://github.com/boostorg/leaf/issues/30#issuecomment-933796969  

Right, the `context` documentation I linked talks about that, I guess you're saying it isn't prominent enough. Probably needs to go in the tutorial, in the section on the error communication model.
