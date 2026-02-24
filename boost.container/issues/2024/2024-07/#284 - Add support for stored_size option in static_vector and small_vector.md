# #284 - Add support for stored_size option in static_vector and small_vector [Closed]

> Username: Lastique  
> Created at: 2024-07-08 17:39:52 UTC  
> Updated at: 2024-10-12 22:30:45 UTC  
> Closed at: 2024-10-12 22:30:45 UTC  
> Url: https://github.com/boostorg/container/issues/284  

Both `static_vector` and `small_vector` could benefit from supporting the `stored_size` option that is supported by the base `vector` container. `static_vector` and `small_vector` are usually used for small numbers of elements, and it makes sense to be able to use a smaller type to store the number of elements in the container.  
  
Also, just as an additional idea, `static_vector` could automatically use a smaller size type depending on the magnitude of the specified capacity. But my main request is still the support for explicit `stored_size` option.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-07-08 18:07:53 UTC  
> Url: https://github.com/boostorg/container/issues/284#issuecomment-2214855097  

BTW, there is a documentation bug:  
  
https://github.com/boostorg/container/blob/3ed1c76efdce0585a4ff6f56fbca25e9559955ae/include/boost/container/options.hpp#L403  
  
here it says `stored_size` is supported, but it actually is not:  
  
https://github.com/boostorg/container/blob/3ed1c76efdce0585a4ff6f56fbca25e9559955ae/include/boost/container/options.hpp#L393-L394  
  
`stored_size` is also not listed as supported in the QuickBook documentation.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-10-12 22:30:45 UTC  
> Url: https://github.com/boostorg/container/issues/284#issuecomment-2408718007  

Thanks for the proposal. Implemented in commits https://github.com/boostorg/container/commit/13603d7de15d8b410743173664cd579d38e221a9 and https://github.com/boostorg/container/commit/af58c7d226553d52ebf10dba259d99d66936cd6c. To be released with Boost 1.87.
