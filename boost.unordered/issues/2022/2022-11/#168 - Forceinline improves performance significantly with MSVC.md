# #168 - Forceinline improves performance significantly with MSVC [Closed]

> Username: sbsce  
> Created at: 2022-11-21 08:25:17 UTC  
> Updated at: 2024-02-15 17:27:50 UTC  
> Closed at: 2024-02-15 17:27:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/168  

While testing `boost::unordered_flat_set`, I noticed my code is reliably running over 10% faster if I `__forceinline` all the function calls that the `boost::unordered_flat_set` makes in my hot path. My hot path is only doing `.contains()`, so anything called by `.contains()`, including the `.contains` itself, is where I added `__forceinline`. So that in my own code where I call `.contains()`, looking at the disassembly there is no `call` anywhere any more, it's fully inlined. I think I had to add `__forceinline` to 6 functions inside boost code.  
  
It is a bit inconvenient to manually add `__forceinline` to all those functions though - it's definitely worth the 10% performance gain, but I am quite sure that the next time I update boost in a few years, I'll forget to apply these changes again, and then my performance will be worse.   
  
Assuming you don't want to add `__forceinline` to those functions by default, could there maybe some define like `BOOST_FORCEINLINE_UNORDERED_SET` that automatically enables forceinlining all the important functions?  
  
I am already compiling with maximum optimization level of MSVC, so by default it doesn't want to inline it, MSVC often needs to be forced to inline stuff.

---

## Comment 1

> Username: joaquintides  
> Created at: 2022-11-21 08:28:00 UTC  
> Updated at: 2022-11-21 08:28:19 UTC  
> Url: https://github.com/boostorg/unordered/issues/168#issuecomment-1321683764  

Can you list those 6 Boost-internal functions you inlined?

---

## Comment 2

> Username: sbsce  
> Created at: 2022-11-21 08:39:51 UTC  
> Updated at: 2022-11-21 08:43:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/168#issuecomment-1321697734  

Sure, here's what I had to change to get it fully inlined, I changed it one by one until none of the functions individually showed up in the VS profiler any more and the disassembly of my hot path no longer contained any `call` instruction to boost:  
  
![image](https://user-images.githubusercontent.com/38058738/203003795-af405d24-bfbc-4b40-b718-5ba214b7b63c.png)  
![image](https://user-images.githubusercontent.com/38058738/203003479-ee4ba8e9-ac44-4afa-8443-2ba876a6e1e8.png)  
![image](https://user-images.githubusercontent.com/38058738/203003588-6d82a025-8871-4cc0-aba9-24d0e5ba1e87.png)  
  
FORCEINLINE is a globally defined macro I have that's just doing `__forceinline`.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-21 13:46:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/168#issuecomment-1322090199  

Force-inlining the destructor and the copy assignment is a bit odd. The rest seem sensible.
