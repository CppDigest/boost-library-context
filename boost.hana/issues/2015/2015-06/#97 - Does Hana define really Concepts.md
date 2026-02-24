# #97 - [Documentation] Does Hana define really Concepts? [Closed]

> Username: viboes  
> Created at: 2015-06-13 13:25:30 UTC  
> Updated at: 2015-06-28 09:47:52 UTC  
> Closed at: 2015-06-28 09:47:52 UTC  
> Url: https://github.com/boostorg/hana/issues/97  

The documentation uses the Concept word which has a concrete meaning on C++ ( and all of us are waiting the next version including Concepts).  
  
I find this confusing. I would prefer the use of type class, as this is exactly what Hana defines,of course adapted to C++ and simulated via a library with a specific protocol.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 17:54:51 UTC  
> Url: https://github.com/boostorg/hana/issues/97#issuecomment-111736785  

Concepts have been around in C++ long before the advent of the language feature of the same name. As you are surely aware of, they were used in the SGI STL and they are used everywhere in Boost in almost the exact same way as Hana does. In particular, Hana's use of concepts is very similar to Boost.Fusion's use of concepts, since both libraries use tag dispatching to provide custom implementations.  
  
Hana used to talk about Type classes instead of Concepts. It was suggested on the Boost.Devel mailing list that I use the word Concept, since all C++ programmers would recognize that term in no time. I am not going back to type classes. However, I could write up a small section explaining what we mean by Concepts in Hana, and how they are also similar to Type Classes from Haskell. Would you find that an acceptable resolution?

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-14 12:14:02 UTC  
> Url: https://github.com/boostorg/hana/issues/97#issuecomment-111820401  

What about the the term `Mapped Concepts`or `tagged Concepts`?

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-14 12:27:38 UTC  
> Url: https://github.com/boostorg/hana/issues/97#issuecomment-111822040  

Well, I think just _concept_ works fine really. It's just the same as in Fusion, isn't it? Is it possible that what's disturbing you is the fact that algorithms are grouped in the same header files as the concept that defines them? Normally, say in Fusion, you have a bunch of concepts, and then you have a bunch of intrinsics, all in separated header files. And the concepts refer to intrinsics: "in order to model AssociativeSequence, you must define `at_key`". In Hana, the header organization is simply made such that you don't have to include all the intrinsics separately.
