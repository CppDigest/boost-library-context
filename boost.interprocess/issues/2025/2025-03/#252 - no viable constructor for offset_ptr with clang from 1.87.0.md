# #252 - [regression] no viable constructor for offset_ptr with clang from 1.87.0 [Closed]

> Username: wanghan02  
> Created at: 2025-03-03 09:45:56 UTC  
> Updated at: 2026-01-05 00:37:13 UTC  
> Closed at: 2026-01-05 00:37:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/252  

Example code could be found as below or on [godbolt](https://godbolt.org/z/rzonsWv9M). The example compiles fine with VS/gcc + boost 1.87.0 or clang + boost 1.86.0. It fails with clang + boost 1.87.0.  
  
```  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <map>  
  
using namespace boost::interprocess;  
  
managed_shared_memory g_segment(create_only, "shared", 65536);  
  
template<typename T>  
struct shared_allocator: allocator<T, managed_shared_memory::segment_manager>  
{  
    using base_=allocator<T, managed_shared_memory::segment_manager>;  
      
    shared_allocator(): base_(g_segment.get_segment_manager()) {}  
  
    template<typename U>  
    struct rebind final {  
        using other = shared_allocator<U>;  
    };  
};  
  
std::map<int, int, std::less<int>, shared_allocator<std::pair<int const, int>>> mapnn;  
```

---

## Comment 1

> Username: wanghan02  
> Created at: 2025-03-05 10:34:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/252#issuecomment-2700517099  

It seems that the constructor was deleted in this [commit](https://github.com/boostorg/interprocess/commit/39aed2f41b45e5866dd8039aadb3808fd25659cd).  
  
```  
 //!Constructor from other offset_ptr. Only takes part in overload resolution 1Has a conversation.  
   //!if PointedType* is constructible from T2* other than via a conversion (e.g. cast to a derived class). Never throws.  
   template<class T2>  
   BOOST_INTERPROCESS_FORCEINLINE explicit offset_ptr(const offset_ptr<T2, DifferenceType, OffsetType, OffsetAlignment> &ptr  
             #ifndef BOOST_INTERPROCESS_DOXYGEN_INVOKED  
             , typename ipcdetail::enable_if_c<  
                !::boost::is_convertible<T2*, PointedType*>::value && ::boost::is_constructible<T2*, PointedType*>::value  
             >::type * = 0  
             #endif  
             ) BOOST_NOEXCEPT  
      : internal(ipcdetail::offset_ptr_to_offset<OffsetType>(static_cast<PointedType*>(ptr.get()), this))  
   {}  
```

---

## Comment 2

> Username: gharveymn  
> Created at: 2025-04-13 00:08:47 UTC  
> Url: https://github.com/boostorg/interprocess/issues/252#issuecomment-2799238139  

Here's another case which fails with all the major compilers:  
  
This change makes allocators using this pointer (like `adaptive_pool`) no longer meet the requirements of _Allocator_ since it is no longer possible to cast `std::allocator_traits<A>::void_pointer` to `std::allocator_traits<A>::pointer`.  
  
[Godbolt](https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:58,endLineNumber:9,positionColumn:58,positionLineNumber:9,selectionStartColumn:58,selectionStartLineNumber:9,startColumn:58,startLineNumber:9),source:'%23include+%3Cboost/interprocess/allocators/adaptive_pool.hpp%3E%0A%23include+%3Cboost/interprocess/managed_shared_memory.hpp%3E%0A%0Ausing+namespace+boost::interprocess%3B%0Ausing+alloc+%3D+adaptive_pool%3Cint,+managed_shared_memory::segment_manager%3E%3B%0A%0Avoid+f()%0A%7B%0A++typename+std::allocator_traits%3Calloc%3E::void_pointer+vp%3B%0A++static_cast%3Ctypename+std::allocator_traits%3Calloc%3E::pointer%3E+(vp)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:43.171858721535386,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((g:!((h:compiler,i:(compiler:g142,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'1',intel:'0',libraryCode:'0',trim:'1',verboseDemangling:'0'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!((name:boost,ver:'187')),options:'-std%3Dc%2B%2B20+-Wall',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+gcc+14.2+(Editor+%231)',t:'0')),k:50.374037024740915,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+gcc+14.2+(Compiler+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),k:56.828141278464614,l:'3',n:'0',o:'',t:'0')),l:'2',n:'0',o:'',t:'0')),version:4)

---

## Comment 3

> Username: igaztanaga  
> Created at: 2026-01-04 13:51:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/252#issuecomment-3708104809  

Thanks for the report. Certainly the conversions required by Allocator named requirements are not fulfilled. This is not an issue for Boost.Container because it does no use static_cast for such operations. Will reintroduce the explicit constructor.
