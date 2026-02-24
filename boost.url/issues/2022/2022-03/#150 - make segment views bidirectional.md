# #150 - make segment views bidirectional [Closed]

> Username: alandefreitas  
> Created at: 2022-03-25 16:17:43 UTC  
> Updated at: 2022-03-29 23:21:46 UTC  
> Closed at: 2022-03-29 23:21:46 UTC  
> Url: https://github.com/boostorg/url/issues/150  

segments has to be BidirectionalRange because users will commonly want quick access to the last segment (i.e. the "filename").  
  
We need to remove the promise of RandomAccess.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-03-25 16:30:22 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1079196000  

it would be nice to have `first()` and `last()`

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-03-28 18:18:47 UTC  
> Updated at: 2022-03-28 18:19:11 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1080987679  

I'm not sure about the best design here. `segments_encoded_view::iterator` and `segments_view::iterator` are already bidirectional, but they are constant. Making `segments_encoded::iterator` and `segments::iterator` bidirectional would just be replicating a lot of that logic.   
  
Ignoring naming, for now, the only idea I have for not replicating the logic is to get rid of `segments_encoded_view` and `segments_view` (or `segments_encoded` and `segments`) altogether and merge their logic into only two containers. The two containers left would have the non-const functions and act as any other STL container with two kinds of iterators: `iterator` and `const_iterator`, all with the current logic of `segments_encoded_view::iterator` and `segments_view::iterator`.  
  
Is that what we should pursue?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-03-28 18:41:35 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1081009069  

No way, leave the containers and just change the iterators

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-03-28 18:42:07 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1081009489  

`segments_encoded::iterator` is constant as well, no?

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-03-28 18:42:48 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1081010072  

Should I just replicate the logic from `segments_encoded_view::iterator` into `segments_encoded::iterator` or is there something else?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-03-28 18:43:33 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1081011326  

I don't mind making a copy of it, lets get the thing working. We can factor out the increment and decrement algorithms into separate functions if we want, to share the code.

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-03-28 18:44:27 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1081012004  

`segments_encoded::iterator` and `segments_encoded_view::iterator` are both constant. This PR is going to be equivalent to copy-pasting all the logic from `segments_encoded_view::iterator` into `segments_encoded::iterator`.

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-03-28 18:46:27 UTC  
> Url: https://github.com/boostorg/url/issues/150#issuecomment-1081013596  

> I don't mind making a copy of it, lets get the thing working. We can factor out the increment and decrement algorithms into separate functions if we want, to share the code.  
  
OK then.
