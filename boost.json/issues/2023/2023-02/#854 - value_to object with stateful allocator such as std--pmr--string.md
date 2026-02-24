# #854 - value_to object with stateful allocator such as std::pmr::string [Closed]

> Username: star-e  
> Created at: 2023-02-07 15:07:14 UTC  
> Updated at: 2023-06-02 04:28:41 UTC  
> Closed at: 2023-06-02 04:28:41 UTC  
> Url: https://github.com/boostorg/json/issues/854  

How can we construct a `std::pmr::string` using `value_to`, with user-provided `std::pmr::memory_resource*`?  
Here, the object and the json value are using different `memory_resource` sources.  
  
The customization function's signature is `T tag_invoke( const value_to_tag< T >&, const value& )`, which does not take any `Allocator` as parameter. So there is no way to provide the allocator.  
  
It seems we can only construct `std::pmr::string` from `std::pmr::get_default_resource()`?

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-02-07 15:08:53 UTC  
> Url: https://github.com/boostorg/json/issues/854#issuecomment-1420931945  

Yes, it's not currently possible, but the feature that will allow it is being worked on.

---

## Comment 2

> Username: star-e  
> Created at: 2023-02-07 15:12:02 UTC  
> Url: https://github.com/boostorg/json/issues/854#issuecomment-1420937604  

That's great, thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-07 15:53:34 UTC  
> Url: https://github.com/boostorg/json/issues/854#issuecomment-1421003632  

@star-e are you using the global memory resource or are you using a custom resource (like a monotonic resource)? Which memory resource are you actually using?

---

## Comment 4

> Username: star-e  
> Created at: 2023-02-07 16:42:57 UTC  
> Url: https://github.com/boostorg/json/issues/854#issuecomment-1421082736  

I am using a custom resource, such as `std::pmr::synchronized_pool_resource`, not global memory resource.  
I use memory resources from `std::pmr` (monotonic, sync pool, unsync pool), and some custom resource like `counting_memory_resource`.  
They are all derived from `std::pmr::memory_resource`.

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-02-27 08:54:47 UTC  
> Url: https://github.com/boostorg/json/issues/854#issuecomment-1445934075  

Please check out #819.  
Docs for contextual conversions: https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/json/conversion/contextual_conversions.html  
Example of passing allocator to a conversion: https://819.jsondocs.prtest.cppalliance.org/libs/json/doc/html/json/examples.html#json.examples.allocator_aware_conversion
