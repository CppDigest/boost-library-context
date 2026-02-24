# #86 - Is there cycle sort in boost sort? [Open]

> Username: denzor200  
> Created at: 2024-12-07 00:13:01 UTC  
> Updated at: 2024-12-10 18:33:47 UTC  
> Url: https://github.com/boostorg/sort/issues/86  

https://en.wikipedia.org/wiki/Cycle_sort  
?

---

## Comment 1

> Username: spreadsort  
> Created at: 2024-12-08 11:43:36 UTC  
> Url: https://github.com/boostorg/sort/issues/86#issuecomment-2525677176  

No, there is not.  Is there an application that can include Boost and needs a cycle sort?  
  
Note that spreadsort has significantly less swaps than a conventional comparison-based sort of data that isn't partially sorted to start, and can be tuned to have even less, but it will have more than cycle sort.

---

## Comment 2

> Username: Morwenn  
> Created at: 2024-12-08 14:27:13 UTC  
> Url: https://github.com/boostorg/sort/issues/86#issuecomment-2526064282  

If what you need is to perform as few moves as possible, then several sorts of the library have an `indirect_sort` alternative (`indirect_spinsort`, `indirect_flat_stable_sort`, etc.) that creates a vector of iterators, sorts that vector, then performs a minimal number of moves to reorder the original collection (at most 3n/2 moves I suppose). The reordering mechanism works with cycles too, but does not suffer from the quadratic complexity of cycle sort.  
  
Not all comparison sorts from the library have an indirect variant, but it should be possible to build one from `common::indirect` either way.

---

## Comment 3

> Username: fjtapia  
> Created at: 2024-12-10 18:33:46 UTC  
> Url: https://github.com/boostorg/sort/issues/86#issuecomment-2532579487  

Some objects can be costly to move due to their size or internal structure  
(such as data structures within data structures). In sorting algorithms,  
when objects are small, the primary operation is comparison.  
However, as the size or internal complexity increases, the focus shifts to  
the movement of objects. This can strain the data bus, making it  
challenging to handle all the movements, resulting in a significant drop in  
the performance of cache memory.  
It's important to note that an O(n²) algorithm is not necessarily the  
solution. Most sorting tasks involve a larger number of elements, which is  
where the real challenges arise.  
While algorithms like Circle Sort may reduce the number of movements, their  
efficiency diminishes as the number of elements increases. For example,  
Insertion Sort has a time complexity of O(n²) and can take about 20 minutes  
to sort a million numbers, while Quicksort, with a time complexity of O(n  
log n), can accomplish the same task in approximately 0.1 seconds. Thus, we  
can conclude that merely improving the number of moves does not address the  
real issue. As Morwenn has pointed out, when dealing with many objects,  
it's advisable to consider indirect algorithms.  
  
El dom, 8 dic 2024 a las 15:27, Morwenn ***@***.***>)  
escribió:  
  
> If what you need is to perform as few moves as possible, then several  
> sorts of the library have an indirect_sort alternative (indirect_spinsort,  
> indirect_flat_stable_sort, etc.) that creates a vector of iterators,  
> sorts that vector, then performs a minimal number of moves to reorder the  
> original collection (at most 3n/2 moves I suppose). The reordering  
> mechanism works with cycles too, but does not suffer from the quadratic  
> complexity of cycle sort.  
>  
> Not all comparison sorts from the library have an indirect variant, but it  
> should be possible to build one from common::indirect either way.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/86#issuecomment-2526064282>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GH4TLRLEZJ33NYCLNT2ERJNPAVCNFSM6AAAAABTFUDOX6VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDKMRWGA3DIMRYGI>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>
