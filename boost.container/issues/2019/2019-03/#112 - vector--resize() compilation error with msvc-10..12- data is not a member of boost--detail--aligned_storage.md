# #112 - vector::resize() compilation error with msvc-10..12: data is not a member of boost::detail::aligned_storage... [Closed]

> Username: awulkiew  
> Created at: 2019-03-29 01:23:17 UTC  
> Updated at: 2019-04-24 20:52:56 UTC  
> Closed at: 2019-04-24 20:52:56 UTC  
> Url: https://github.com/boostorg/container/issues/112  

Detected in Geometry R-tree tests. See https://www.boost.org/development/tests/develop/developer/geometry-index.html for msvc-10..12 x64 on x64, e.g.: https://www.boost.org/development/tests/develop/developer/output/teeks99-09-m-win2012R2-32on64-boost-bin-v2-libs-geometry-index-test-rtree-interprocess-rtree_interprocess_rstar_dyn-test-msvc-12-0-dbg-async-excpt-on-thrdp-wn32-thrd-mlt.html  
  
For convenience, the error is:  
```  
D:\teeks99-09\run\boost_root\boost/container/detail/advanced_insert_int.hpp(132) : error C2039: 'data' : is not a member of 'boost::detail::aligned_storage::aligned_storage_imp<24,8>'  
        D:\teeks99-09\run\boost_root\boost/container/detail/advanced_insert_int.hpp(129) : while compiling class template member function 'void boost::container::dtl::insert_value_initialized_n_proxy<boost::container::new_allocator<T>,T *>::copy_n_and_update(Allocator &,Iterator,unsigned int) const'  
        with  
        [  
            T=child_contents  
,            Allocator=boost::container::new_allocator<child_contents>  
,            Iterator=child_contents *  
        ]  
        D:\teeks99-09\run\boost_root\boost/container/vector.hpp(2983) : see reference to function template instantiation 'void boost::container::dtl::insert_value_initialized_n_proxy<boost::container::new_allocator<T>,T *>::copy_n_and_update(Allocator &,Iterator,unsigned int) const' being compiled  
        with  
        [  
            T=child_contents  
,            Allocator=boost::container::new_allocator<child_contents>  
,            Iterator=child_contents *  
        ]  
```  
  
The `vector::resize()` is called in the R-tree here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/rstar/choose_next_node.hpp#L83  
`container_from_elements_type` is defined here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/rtree/node/node_elements.hpp#L97  
and is simply  
`boost::container::vector<child_contents>`  
Element `child_contents` is `boost::tuple<size_t, double, double>`.  
  
I tired with type different than `boost::tuple<...>` but of the same size (tuple elements as members), the result is the same. However synthetic test, so creating a vector of elements of the same size and alignment and resizing it outside the R-tree code passes. Funnily enough (in the R-tree) creating a vector of desired size with ctor instead of creating wieh default ctor and resizing it works, so instead of this:  
```  
typename rtree::container_from_elements_type<children_type, child_contents>::type children_contents;  
children_contents.resize(children_count);  
```  
this:  
```  
typename rtree::container_from_elements_type<children_type, child_contents_t>::type  
    children_contents(children_count);  
```  
  
I'll try to prepare a limited/synthetic case for this issue but in the meanwhile maybe do you have an idea why this may be the case?

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-03-29 13:26:57 UTC  
> Url: https://github.com/boostorg/container/issues/112#issuecomment-477996893  

It seems that it doesn't have to be x64 mode, it also fails with msvc-12 in x86 mode e.g.:  
https://www.boost.org/development/tests/develop/developer/output/teeks99-09-m-win2012R2-32on64-boost-bin-v2-libs-geometry-index-test-rtree-interprocess-rtree_interprocess_rstar_dyn-test-msvc-12-0-dbg-async-excpt-on-thrdp-wn32-thrd-mlt.html  
surprisingly this test passes with msvc-10 and 11.  
  
FYI, I modified the code by replacing `resize()` with ctor so these errors will probably disappear.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-03-29 21:43:56 UTC  
> Url: https://github.com/boostorg/container/issues/112#issuecomment-478159111  

No idea of what the problem might be. If you provide me a simplified test I could try to understand how MSVC  10-12 does not like the code

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-03-31 22:35:05 UTC  
> Url: https://github.com/boostorg/container/issues/112#issuecomment-478391823  

After heavy digging I was able to write a simple failing test. There are some specific conditions for the error to happen. I tried to simplify the code and destille the problem as much as I could. However the limit here is Container and Variant interface. I suspect it's possible to simplify it even more by checking the internals of both libraries. I hope the code below will at least make your day. ;)  
  
```  
#include <boost/container/vector.hpp>  
#include <boost/variant.hpp>  
  
// conditions:  
// sizeof(foo) == sizeof(bar)  
// same number of members  
// memory model doesn't matter  
// both variant must be created and vector::resize() called  
  
struct foo  
{  
    void * a;  
};  
  
struct bar  
{  
//    float a; // error with x86  
    double a; // error with x64  
};  
  
int main()  
{  
    boost::variant<foo> root; // without it ok  
  
    boost::container::vector<bar> vec;  
    children_contents.resize(0); // without it ok  
}  
```

---

## Comment 4

> Username: awulkiew  
> Created at: 2019-04-01 16:20:06 UTC  
> Updated at: 2019-04-01 17:46:22 UTC  
> Url: https://github.com/boostorg/container/issues/112#issuecomment-478646880  

Actually I found a fix for this. It seems that the compiler tries to instantiate `boost::aligned_storage` instead of `boost::container::dtl::aligned_storage` in this specific case. The former doesn't have `data` member variable, it has `address()` member function. So it can be fixed or rather worked around by prefixing `aligned_storage` with `dtl::` namespace.  
  
Side question, why are you using your own `aligned_storage`? AFAIU the "common" one is not a big dependency and it's used in `container::node_handle` anyway.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2019-04-24 20:52:55 UTC  
> Url: https://github.com/boostorg/container/issues/112#issuecomment-486421384  

Closing the issue after merging:  
  
https://github.com/boostorg/container/commit/28e8d6da78a163d71390eb1b8b16724495013529
