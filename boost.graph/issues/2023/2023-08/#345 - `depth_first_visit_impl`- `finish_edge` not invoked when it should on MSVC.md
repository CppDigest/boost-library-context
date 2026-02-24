# #345 - `depth_first_visit_impl`: `finish_edge` not invoked when it should on MSVC [Open]

> Username: jrsala  
> Created at: 2023-08-25 16:42:25 UTC  
> Updated at: 2023-11-06 02:47:32 UTC  
> Url: https://github.com/boostorg/graph/issues/345  

Lines 94-104 in depth_first_search.hpp:  
  
```c++  
    template < typename E, typename G, typename Vis >  
    void call_finish_edge(Vis& vis, E e, const G& g)  
    { // Only call if method exists  
#if ((defined(__GNUC__) && (__GNUC__ > 4)               \  
         || ((__GNUC__ == 4) && (__GNUC_MINOR__ >= 9))) \  
    || defined(__clang__)                               \  
    || (defined(__INTEL_COMPILER) && (__INTEL_COMPILER >= 1200)))  
        do_call_finish_edge< has_member_function_finish_edge< Vis, void,  
            boost::mpl::vector< E, const G& > >::value >::call_finish_edge(vis,  
            e, g);  
#else  
        do_call_finish_edge< has_member_function_finish_edge< Vis,  
            void >::value >::call_finish_edge(vis, e, g);  
#endif  
    }  
```  
  
On MSVC, the section after the `#else` will not call `Vis::finish_edge` even when it is available and has the correct signature because `has_member_function_finish_edge<Vis, void>::value` is `false`. I think this is because instead of checking that `Vis::finish_edge` exists and has return type `void` as was seemingly intended, it in facts checks that it has the exact signature `void()`. So on MSVC, even when providing a visitor type with a correct `void finish_edge(E, const G&)`, it is never called.  
  
I don't know how to fix this because I don't know why it was determined that some compilers should be treated differently here.  
  
I circumvented the bug in my code by adding a dummy `void finish_edge() {}` member function in my visitor type next to the actual `void finish_edge(E, const G&)` function just so the member function detection would yield `true`.  
  
Alternatively this could be a bug in boost/tti/has_member_function.hpp but I guess that's less likely.

---

## Comment 1

> Username: marvin-the-mathematician  
> Created at: 2023-11-01 16:28:27 UTC  
> Updated at: 2023-11-01 16:29:47 UTC  
> Url: https://github.com/boostorg/graph/issues/345#issuecomment-1789266388  

I am having this problem with Visual Studio 2022 and Boost 1.82.0, but worse :-( Since I am using a EventVisitorList (https://www.boost.org/doc/libs/1_82_0/libs/graph/doc/EventVisitorList.html) with make_dfs_visitor I have no alternative, but to ditch that, write my own visitor and use the workaround outlined above, which I don't really want to do. A rather annoying bit of meta-programming that is getting in the way of making a simple call! Why not just call the finish_edge member function and let the compiler decide whether it actually exists or not?

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2023-11-06 02:47:32 UTC  
> Url: https://github.com/boostorg/graph/issues/345#issuecomment-1794002152  

There is a lot of very old code in this library. We're not supporting 'old' compilers any more, so probably the best thing to do is remove the `#if-else` altogether, or at least try to push it up into a helper utility outside the library. If you make a PR to fix it then I will see to fast-track approving it.
