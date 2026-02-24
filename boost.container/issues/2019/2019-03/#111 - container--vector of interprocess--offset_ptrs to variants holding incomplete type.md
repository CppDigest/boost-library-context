# #111 - container::vector of interprocess::offset_ptrs to variants holding incomplete type. [Closed]

> Username: awulkiew  
> Created at: 2019-03-26 18:46:50 UTC  
> Updated at: 2019-03-28 22:07:31 UTC  
> Closed at: 2019-03-28 22:07:31 UTC  
> Url: https://github.com/boostorg/container/issues/111  

Recently I was notified that code compiling some time ago and using `container::vector` stopped compiling at some point in the past (not sure when). The code I'm talking about is Geometry R-tree with Interprocess allocator.  
  
The R-tree has internal and leaf nodes. Internal nodes contain (more or less) vector of pointers to variants containing either internal or leaf nodes. So when the vector type is defined the internal node type is incomplete but this is fine since pointers are stored.  
  
At some point this code stopped compiling with Clang in gnu++98 and c++03 modes when the pointer is `interprocess::offset_ptr` taken from `interprocess::allocator`. It works with `container::new_allocator` though. I was able to limit the test case (see below). The original errors reported for Geometry can be found [here](https://www.boost.org/development/tests/develop/developer/geometry-index.html). These are the tests **rtree_interprocess_XXX_dyn**, e.g. [this one](https://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-geometry-index-test-rtree-interprocess-rtree_interprocess_linear_dyn-test-clang-linux-6-0~gnu++98-debug-threadapi-pthread-threading-multi-visibility-hidden.html).  
  
I didn't dig much but it seems that `variant` checks some `type_traits` after it's instantiated by vector's check of `boost::move_detail::is_convertible`:  
  
    static const bool value = sizeof(dispatch(trigger())) == sizeof(true_t);  
  
and the instantiation is done at `trigger()`. The type passed to the type traits is expected to be complete. It seems all versions of Clang are affected. I was able to reproduce it with 3.8.  
  
Do you have some idea about a workaround for this?  
  
Below is the limited test case in case you wanted to play with it.  
  
    #include <boost/container/vector.hpp>  
    #include <boost/interprocess/managed_shared_memory.hpp>  
    #include <boost/interprocess/allocators/allocator.hpp>  
    #include <boost/variant.hpp>  
  
    template <typename T>  
    struct wrapper  
    {  
        T v;  
    };  
  
    template <typename Alloc>  
    struct internal_node  
    {  
        typedef boost::variant<internal_node<Alloc> > node_type;  
  
        typedef typename boost::container::allocator_traits  
            <  
                Alloc  
            >::template rebind_alloc<node_type> node_alloc_type;  
  
        typedef typename boost::container::allocator_traits  
            <  
                node_alloc_type  
            >::pointer node_pointer_type;  
  
        typedef wrapper  
            <  
                node_pointer_type  
            > elem_type;  
  
        typedef typename boost::container::allocator_traits  
            <  
                node_alloc_type  
            >::template rebind_alloc<elem_type> elem_alloc_type;  
  
        template <typename A>  
        internal_node(A const& a) : elems(elem_alloc_type(a)) {}  
  
        boost::container::vector<elem_type, elem_alloc_type> elems;  
    };  
  
    int main()  
    {  
        {  
            typedef boost::container::new_allocator<int> alloc;  
            alloc al;  
  
            internal_node<alloc> n(al);  
        }  
  
    #ifndef DISABLE_ERROR  
        {  
            namespace bi = boost::interprocess;  
            typedef bi::allocator  
                <  
                    int, bi::managed_shared_memory::segment_manager  
                > shmem_alloc;  
  
            bi::managed_shared_memory segment(bi::create_only, "shmem", 65535);  
            shmem_alloc sh_al(segment.get_segment_manager());  
  
            internal_node<shmem_alloc> n(sh_al);  
        }  
    #endif  
    }

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-03-26 21:43:50 UTC  
> Url: https://github.com/boostorg/container/issues/111#issuecomment-476864757  

Maybe commit:  
  
https://github.com/boostorg/container/commit/0691018321f03aad662d7d2ae5b3e060c7109e3c  
  
can fix the issue.

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-03-27 16:35:26 UTC  
> Url: https://github.com/boostorg/container/issues/111#issuecomment-477242719  

This solves the problem for me. Thank you!  
  
I think the issue may be closed. I'll check the regression matrix in a while and see if the errors disappeared. In the worst case I'll reopen it but I doubt this will be needed.

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-03-27 17:35:57 UTC  
> Updated at: 2019-03-27 17:37:33 UTC  
> Url: https://github.com/boostorg/container/issues/111#issuecomment-477271378  

Do you plan to release it with 1.70?

---

## Comment 4

> Username: igaztanaga  
> Created at: 2019-03-28 22:07:31 UTC  
> Url: https://github.com/boostorg/container/issues/111#issuecomment-477789054  

Yes, just got permission from the release manager and I've merged the commit into the master branch.
