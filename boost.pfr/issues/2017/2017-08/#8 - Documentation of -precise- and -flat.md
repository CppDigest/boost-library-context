# #8 - Documentation of "precise" and "flat" [Closed]

> Username: eldiener  
> Created at: 2017-08-13 03:03:27 UTC  
> Updated at: 2017-08-22 05:32:30 UTC  
> Closed at: 2017-08-22 05:32:30 UTC  
> Url: https://github.com/boostorg/pfr/issues/8  

I think you should document what you mean by "precise" and "flat" in your library as part of an Overview section and before the Tutorial. It is very important for people to understand this as early as possible in the doc.

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-08-13 18:41:26 UTC  
> Url: https://github.com/boostorg/pfr/issues/8#issuecomment-322059344  

Added a small example into the motivation section.  
  
Any advices on improving docs are welcomed! :)

---

## Comment 2

> Username: eldiener  
> Created at: 2017-08-13 19:20:41 UTC  
> Url: https://github.com/boostorg/pfr/issues/8#issuecomment-322061509  

I would have written:  
  
"The two different types of reflection are:  
1) Precise reflection, where each type is represented as it actually exists, even if the type is itself a user-defined type.  
2) Flat reflection, where user-defined types are represented by their individual field types, and all other types are represented as it actually exists.  
  
As an example:"  
  
followed by the code example you give. It is almost always better to remove all shortcuts in explaining concepts, and write everything out in the most explanatory way. What you know and understand intuitively is hardly intuitive to your end-users. This is especially true when an end-user needs to understand and justify to himself the usefulness of your library. You might, if you wanted to, even to go on and explain to the end-user why you even included the flat functionality in your library as opposed keeping everything precise. But that entails design decisions which you might not want to explain in the docs, but would rather justify during a review.

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-08-22 05:32:30 UTC  
> Url: https://github.com/boostorg/pfr/issues/8#issuecomment-323923443  

Thanks for the advices and for improving the introduction.
