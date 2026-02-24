# #95 - [Question] The detailled definition of insert, is not hiding a concept? [Closed]

> Username: viboes  
> Created at: 2015-06-13 13:15:44 UTC  
> Updated at: 2015-06-15 19:34:24 UTC  
> Closed at: 2015-06-15 19:34:24 UTC  
> Url: https://github.com/boostorg/hana/issues/95  

There a re a lot of symbols at the Hana level as all the functions are on the boost::hana namespace.  
In particular we have that insert works for Sequence, Map and for Set but have different signature.  
  
IIUC the implementation needs some kind of internal implementation shared between the all the type classes and data types that implement this function is some way.   
  
That is not described in the documentation, and I believe that this hides a concept.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 17:47:05 UTC  
> Url: https://github.com/boostorg/hana/issues/95#issuecomment-111736323  

> IIUC the implementation needs some kind of internal implementation shared between the all the type classes and data types that implement this function is some way.  
  
That is correct; `Map`, `Set` and `Sequence` all share the same `insert` "facade" function object.  
  
> That is not described in the documentation,   
  
Indeed, the fact that they all share the same facade function object is an implementation detail, and that should not be documented.  
  
> and I believe that this hides a concept.  
  
It is possible that some kind of `Insertable` concept is hiding somewhere, but it would be helpful if you could provide something more precise. For example, what should be the functions associated to this concept and what should be the laws it respects?  
  
Also note that I plan to revisit the interface and implementation of associative sequences as part of this year's Google Summer of Code project, so I might find this `Insertable` concept through this.

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-14 12:16:26 UTC  
> Url: https://github.com/boostorg/hana/issues/95#issuecomment-111820840  

When I see an interface I think always as how the user can extend this interface. How the user can define a new Mapped Concept that has a insert function also?

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-14 12:23:52 UTC  
> Url: https://github.com/boostorg/hana/issues/95#issuecomment-111821826  

Well he can do so in his own namespace, if that's what you mean. Of course, you can't highjack Hana's namespace to put your own names in it, but that's nothing new.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-15 19:34:24 UTC  
> Url: https://github.com/boostorg/hana/issues/95#issuecomment-112182225  

I'm going to close this in favour of #111, which is defining a proper AssociativeSequence concept. Such a concept would most likely include the `insert` function, which seems to be the main concern here.
