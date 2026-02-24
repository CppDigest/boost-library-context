# #120 - flat_map/flat_set with small_vector as container requires explicit allocator-type [Closed]

> Username: tobias-loew  
> Created at: 2019-05-17 12:31:56 UTC  
> Updated at: 2019-09-12 09:26:45 UTC  
> Closed at: 2019-05-20 08:53:27 UTC  
> Url: https://github.com/boostorg/container/issues/120  

The latest changes to the allocator handling in small_vector now requires that the allocator-type has to be specified explicitely when used with flat_map/set:  
  
This line doesn't compile anymore:  
boost::container::flat_map<int, double, std::less<int>, boost::container::small_vector<std::pair<int, double>,17>>  my_map;  
  
But this one does:  
boost::container::flat_map<int, double, std::less<int>, boost::container::small_vector<std::pair<int, double>,17, boost::container::new_allocator<std::pair<int, double>>>>  my_map;  
  
  
The reason for this is that in container_rebind.hpp:60ff  
  
   //Needed for non-conforming compilers like GCC 4.3  
   template <template <class, std::size_t, class> class Cont, typename V, std::size_t N, typename A, class U>  
   struct container_rebind<Cont<V, N, A>, U>  
   {  
      typedef Cont<U, N, typename allocator_traits<A>::template portable_rebind_alloc<U>::type> type;  
   };  
  
  
The template doesn't use the "real_allocator" template to figure out the allocator but uses the small_vector's allocator-default "void".  
  
Tobias

---

## Comment 1

> Username: tobias-loew  
> Created at: 2019-05-17 14:02:12 UTC  
> Url: https://github.com/boostorg/container/issues/120#issuecomment-493464840  

Furthermore I came over another problem with custom allocators:  
I am using the aligned allocator from http://blogs.msdn.com/b/vcblog/archive/2008/08/28/the-aligned_allocator.aspx (from Stephan T. Lavavej) which worked fine with small_vector.  
But with the step from Boost 1.68 to 1.70 I had to add a specialization  
  
```  
template<std::size_t Alignment>  
class aligned_allocator<void, Alignment> {  
  
public:  
	typedef void value_type;  
	typedef void* pointer;  
	typedef const void* const_pointer;  
  
	template<class U>  
	struct rebind {  
		typedef aligned_allocator<U, Alignment> other;  
	};  
};  
```  
  
to make the code compile again.  
I'm using VS 2019, i.e. MSVC 19.20

---

## Comment 2

> Username: tobias-loew  
> Created at: 2019-05-18 14:47:46 UTC  
> Url: https://github.com/boostorg/container/issues/120#issuecomment-493682557  

The container rebind issue seems to be already fix. Thanks

---

## Comment 3

> Username: tobias-loew  
> Created at: 2019-05-20 08:53:27 UTC  
> Updated at: 2019-09-12 09:26:45 UTC  
> Url: https://github.com/boostorg/container/issues/120#issuecomment-493897058  

The problem with rebind are due to the non-type template parameter for the alignment of aligned_allocator: the helper-template for doing the rebind only has specializations for class-type templates.   
Neither gcc, clang or msvc is able to rebind such an allocator with their std::allocator_trait, ~~though I couldn't find any restriction on the kind of template parameters for allocators in the standard.~~ the standard enables automatic rebind only for allocators _with only class-type template arguments_.  
  
I fixed my problem by replacing the size_t parameter by std::integral_type
