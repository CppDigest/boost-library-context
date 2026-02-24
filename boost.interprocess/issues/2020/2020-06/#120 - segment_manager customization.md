# #120 - segment_manager customization [Closed]

> Username: msmolensky  
> Created at: 2020-06-29 05:27:58 UTC  
> Updated at: 2021-02-21 21:21:35 UTC  
> Closed at: 2021-02-20 23:15:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/120  

I needed to extend segment_manager to intercept allocate(). My use case was to check when the used memory exceeds certain threshold and handle it somehow. I could not find a good way to do it.  
  
One way was to have MyClass to point to segment_manger and check the threshold in MyClass::allocate(), e.g.  
  
class MyClass  
{  
public:  
  void* allocate(...)   
  {  
   _seg_man->alocate(...);  
   if( _seg_man->get_free_memory() > _threshold) ...  
  }  
  
private:  
  segment_manager<...>* _seg_man;  
  size_type _threshold;  
};  
  
This works for simple allocate()/deallocate() but does not work if MyClass is passed as the segment_manager parameter to boost::interprocess provided pools. The reason is the pools expect that segment_manager is derived from memory_algorithm. There are static_cast's form memory_algorithm to segment_manger so static_cast\<MyClass>( memory_algorithm) won't compile.  
  
Another way was to derive MyClass from segment_manager. This also does not work for stateful classes because the segment manager is allocated in the mapped segment and MyClass's data would overlap with the memory that memory_algorithm considers as free.  
  
MyClass : public segment_manager<...>  
{  
  size_type _thershold; // overlaps with memory_algorithm's free memory  
};  
  
There is also no way to adjust reserved_bytes via segment_manger so that memory_algorithm won't use that memory.  
  
Another way was to derive MyClass from memory_algorithm and specialize segment_manager with it, e.g.  
  
MyClass : public rbtree_best_fit<...>  
{  
public:  
  void* allocate(...)  
  {  
     rbtree_best_fit<...>::allocate(...);  
     if( seg_man->get_free_memory() > _threshold) ...  
  }  
  
  void set_threshold(...)  // inaccessible under private inheritance  
  
private:  
  size_type _threshold;  
};  
  
segment_manager<... MyClass ...>  
  
This would create segment_manager correctly but due to the fact that segment_manager_base derives privately from MemoryAlgorithm (that is from MyClass), there is no way to set MyClass up: to pass additional parameters to constructor or use setters.  
  
I found no better way than to modify the boost::interprocess code to change segment_manager_base inheritance to protected from private and derive publicly from segment_manger to access MyClass, e.g.  
  
MyClass ... // as above  
  
segment_manger_base : protected MemoryAlgorithm {...}; // changed private to protected  
  
MySegmentManager : public segment_manager< ... MyClass ... >  // stateless, won't corrupt mapped memory  
{  
public:  
  using set_threshold;  
};  
  
I am wondering if there is a better way to do it and whether or not it makes sense to change boost::interprocess code to use protected inheritance in segment_manager_base  
  
Thank you

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-20 23:14:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/120#issuecomment-782763806  

I didn't expect users would write their own algorithms, but in that case users should be able to obtain a reference to the memory algorithm to set up parameters. The fact that the segment manager base derives from memory algorithm is an implementation detail and a user should not rely on it..  
  
The easiest way is to have access to the memory algorithm is to add a `get_memory _algorithm()` member function to the segment manager. Obviously, only advanced users should use this access.

---

## Comment 2

> Username: msmolensky  
> Created at: 2021-02-21 00:10:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/120#issuecomment-782769241  

So will get_memory_algorithm() be added?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2021-02-21 21:21:35 UTC  
> Url: https://github.com/boostorg/interprocess/issues/120#issuecomment-782928901  

The develop branch commit that closed this issue adds get_memory_algorithm() feature.
