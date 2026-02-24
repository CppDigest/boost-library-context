# #11 - poly_collection tests expect move-assigned-from state of containers to be empty, despite non-POCMA allocators [Closed]

> Username: BillyONeal  
> Created at: 2019-02-05 20:43:30 UTC  
> Updated at: 2019-02-06 23:55:53 UTC  
> Closed at: 2019-02-06 11:24:40 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/11  

On line 124 of test_construction.cpp ( https://github.com/boostorg/poly_collection/blob/develop/test/test_construction.cpp#L124 ), there is a move of assignment of a rooted_poly_collection (which contains a std::unordered_map). Then there's an assertion that p2 is empty, on the basis of the moved-from state of that unordered_map. For a POCMA allocator, it makes sense that the only reasonable state in which the library can leave the moved-from container be the empty state, since there we must take the contents of the original container. But for a non-POCMA allocator, where we can't take the contents of the moved-from container, the natural state to leave the source container is containing a bunch of moved-from Ts, meaning it won't be empty.  
  
It seems like maybe the whole block should be guarded like this?  
  
	if (Propagate || AlwaysEqual) {  
		BOOST_TEST(d2 == d3);  
		BOOST_TEST(p2.empty());  
		do_((BOOST_TEST(!p2.template is_registered<Types>()), 0)...);  
	}

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-02-06 11:24:40 UTC  
> Updated at: 2019-02-06 13:57:08 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/11#issuecomment-460989611  

You're right about the standard not mandating that moved-from containers be empty, addressed at d13e8ba7ba4e67a29eb102cb4c700776db86d4fc.  
  
On the other hand, I haven't met a stdlib implementation that did not clear moved-from containers (see, for instance, [libstdc++-v3](http://coliru.stacked-crooked.com/a/c4cb0c9cf865cb75)). Is VS2019 doing otherwise?

---

## Comment 2

> Username: BillyONeal  
> Created at: 2019-02-06 23:55:53 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/11#issuecomment-461236142  

>Is VS2019 doing otherwise?  
  
Yes; I fixed a ton of bugs in how we handled non-POCCA POCMA POCS allocators and one of the consequences is that the moved from container is no longer empty (as there's no reason to make it empty and the caller is likely about to destroy it anyway).
