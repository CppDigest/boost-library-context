# #13 - Functor::list_mcd is not an mcd [Closed]

> Username: viboes  
> Created at: 2014-10-05 11:16:36 UTC  
> Updated at: 2014-10-05 13:46:41 UTC  
> Closed at: 2014-10-05 13:46:41 UTC  
> Url: https://github.com/boostorg/hana/issues/13  

I suggest to rename Functor::list_mcd  to Functor::when_list.

---

## Comment 1

> Username: ldionne  
> Created at: 2014-10-05 13:20:41 UTC  
> Url: https://github.com/boostorg/hana/issues/13#issuecomment-57935780  

I don't understand why it is not an MCD. An MCD takes a minimal set of requirements and provides you with a model of some concept from those minimal requirements. `Functor::list_mcd` requires:  
- A `List` (because it uses `cons` and `nil`)  
- A `Foldable` (because it uses `foldr`)  
  
and gives you a `Functor`. Perhaps you could explain why you think `Functor::list_mcd` is not an MCD?

---

## Comment 2

> Username: viboes  
> Created at: 2014-10-05 13:29:59 UTC  
> Url: https://github.com/boostorg/hana/issues/13#issuecomment-57936044  

Oh, I git it. `List` is not a concrete data type, and so instance using the `list_mcd` needs to define `cons` and `nil`  
Sorry for the noise.

---

## Comment 3

> Username: ldionne  
> Created at: 2014-10-05 13:46:41 UTC  
> Url: https://github.com/boostorg/hana/issues/13#issuecomment-57936523  

No problem. `List` first started as both a data type and a type class, but that got changed. Now, `List` is the type class and `Tuple` is the data type. That being said, it would be useful to be able to create data types with some methods, and then to say "consider that data type as a type class where all instances have to be isomorphic". This would be useful because it would allow us   
- _not_ to create a "dummy" type class like `List`, where all instances are isomorphic  
- and instead focus on creating the canonical, unique up-to-isomorphism instance  
- while still retaining the ability to customize method, and hence have e.g. `boost::fusion::vector` an instance of that "type class"  
  
I'm still trying to grasp exactly how the above would fit into the library.
