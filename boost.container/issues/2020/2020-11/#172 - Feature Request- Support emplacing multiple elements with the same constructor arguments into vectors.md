# #172 - Feature Request: Support emplacing multiple elements with the same constructor arguments into vectors [Open]

> Username: Lastique  
> Created at: 2020-11-27 12:37:19 UTC  
> Updated at: 2020-12-29 19:12:51 UTC  
> Url: https://github.com/boostorg/container/issues/172  

Consider a case when we have a non-default-constructibe type, which we want to store in a vector (any flavor of the supported by Boost.Container). In order to allocate N elements in the vector, one has to create one template object, which is then used to copy-construct elements:  
  
```  
class A  
{  
public:  
    explicit A(int, float);  
};  
  
boost::container::vector<A> vec;  
vec.resize(10, A(42, 3.14f));  
```  
  
This can be problematic if `A` is not copy-constructible or is expensive to copy. A better solution would be to directly emplace N elements from the user-provided constructor arguments:  
  
```  
vec.resize_emplace(10, 42, 3.14f); // calls A(42, 3.14f) 10 times  
```  
  
This construction would be useful also because there is no easy way to achieve this with raw arrays, statically or dynamically allocated. Currently, one has to separately allocate storage and inplace-construct elements, with the careful fallback in case of exception, and do the reverse on destruction.  
  
The same extension could be made for `insert`:  
  
```  
vec.insert_emplace(vec.begin(), 10, 42, 3.14f); // moves elements as necessary and calls A(42, 3.14f) 10 times  
```  
  
This could be emulated by a loop of `emplace` calls, but this is less efficient than the dedicated methods. In particular, the `emplace` loop would unnecessarily move elements and reallocate memory.  
  
PS: The method names are given for illustration, I'm not attached to them.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-12-29 15:25:37 UTC  
> Url: https://github.com/boostorg/container/issues/172#issuecomment-752120737  

I assume you want to to copy arguments for each newly created object, right? Moving seems problematic...  
```  
vec.clear();  
vec.resize(10, arg1, arg2, arg3, ...) //calls value_type(arg1, arg2, arg3) 10 times

---

## Comment 2

> Username: Lastique  
> Created at: 2020-12-29 19:12:51 UTC  
> Url: https://github.com/boostorg/container/issues/172#issuecomment-752210086  

That's a good question. Ideally, I would assume the arguments would be perfectly forwarded, but I can see this could cause problems when inserting more than one element. I suppose, forwarding rvalues as const references when N > 1 is the right way to go. When N == 1 perfect forwarding would make more sense. Or, put it another way, all elements but the last one should be constructed with forwarding rvalues as const references, and the last one - with perfect forwarding.
