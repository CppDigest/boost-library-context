# #139 - flat_map merge and iterators [Closed]

> Username: terrylyons  
> Created at: 2020-01-11 23:37:58 UTC  
> Updated at: 2020-11-14 22:28:30 UTC  
> Closed at: 2020-11-13 23:10:54 UTC  
> Url: https://github.com/boostorg/container/issues/139  

The limited documentation for flat_map and merge seems incorrect. It claims that merge will not invalidate iterators but just move them to the target flat_map. Given the general statements about the data being stored contiguously and in order and using binary search for find this seems implausible!! It looks like a cut and paste job from std::map.  
  
It would be useful to have accurate documentation on flat_map::merge and in particular its complexity in the implimented form.  
  
> template<typename C2>   
  void merge(flat_multimap< Key, T, C2, AllocatorOrContainer > & source);  
Requires: this->get_allocator() == source.get_allocator().  
Effects: Attempts to extract each element in source and insert it into a using the comparison object of *this. If there is an element in a with key equivalent to the key of an element from source, then that element is not extracted from source.  
Postcondition: Pointers and references to the transferred elements of source refer to those same elements but as members of *this. Iterators referring to the transferred elements will continue to refer to their elements, but they now behave as iterators into *this, not into source.

---

## Comment 1

> Username: terrylyons  
> Created at: 2020-11-14 08:03:49 UTC  
> Url: https://github.com/boostorg/container/issues/139#issuecomment-727163798  

I doubt if the complexity is  
<b>Complexity</b>: N log(size() + N) (N has the value source.size())  
It has to be linear in size[] as well as N unless something magic is happening.  
Depending on the implementation it could even be the product!!  That is why it is important. I guess insert invalidates all iterators. As these are clear difference in behaviour to map it seems important to mention it.  
  
  
On 13 Nov 2020, at 23:11, Ion Gaztañaga <notifications@github.com> wrote:  
  
﻿  
  
Closed #139<https://github.com/boostorg/container/issues/139> via 93bbf37<https://github.com/boostorg/container/commit/93bbf37dad31e310f3cfcd874faa4e899cf8931f>.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/container/issues/139#event-3995700558>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/ABQWN62OCVRBYQV2LEMYJL3SPW4IXANCNFSM4KFUSR7Q>.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-11-14 22:28:30 UTC  
> Url: https://github.com/boostorg/container/issues/139#issuecomment-727274093  

You are right. The used merge algorithm is in-place but O(N) (the standard requires O(NlogN) std::inplace_merge).
