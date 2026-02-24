# #155 - clarification of extending documentation [Closed]

> Username: cppljevans  
> Created at: 2015-06-23 08:56:01 UTC  
> Updated at: 2015-06-23 18:06:42 UTC  
> Closed at: 2015-06-23 14:28:52 UTC  
> Url: https://github.com/boostorg/hana/issues/155  

The extending documentation here:  
  
  http://ldionne.com/hana/index.html#tutorial-extending-tag_dispatching  
  
needs some clarification.  For example, it uses an itemized  
list containing:   
  
  in item 2., sentence 1:  
    A function belonging to the public interface of the library  
  in item 2., sentence 2:  
    these functions are the algorithms associated to a  
    concept, like transform or unpack.  
  
However, when reading the sentence 1, it's unclear what's  
the "public interface of the library" means.  Reading  
sentence 2, containing the word, "concept" gives a hint that  
the "public interface" maybe defined here:  
  
  http://ldionne.com/hana/group__group-concepts.html  
  
But that's not much help because there's no mention of  
"public interface", there are just links to  
the actual concepts, such as:  
  
  http://ldionne.com/hana/structboost_1_1hana_1_1Applicative.html  
  
So the reader probably guesses that looking at those links  
may clarify; hence, looking at the Applicative link, he sees:  
  
  Synopsis of methods  
  
which looks like a likely candidate for what could be called  
a "public interface".  In other words, for _Concept_  
Applicative, the "public interface" might be:  
  
  1) ap  
  2) lift      
  
So, the reader guesses that the 1st step in extending the  
library is to select a concept to which his extensiion will  
belong, and then, for each method, a_meth, listed in the "Synopsis of  
methods" for the selected concept, provides an  
implementation, a_meth_impl, of that method, just as  
described item 3 of the itemized list of:  
  
  http://ldionne.com/hana/index.html#tutorial-extending-tag_dispatching  
  
where, instead of a_meth, the less descriptive, xxx, is  
used.  
  
So, if one wants to extend the library with another  
Applicative, he would provide an ap_impl and lift_impl for  
this other Applicative.  
  
To confirm this theory, the reader looks at:  
  
  http://ldionne.com/hana/structboost_1_1hana_1_1Optional.html  
  
noting that Optional models Applicative; hence, the reader  
looks at the source for Optional here:  
  
  https://github.com/ldionne/hana/blob/master/include/boost/hana/optional.hpp  
  
and indeed finds ap_impl and lift_impl on lines 245 and 252.  
  
But then the reader wonders how the compiler can know that  
Optional models  Applicative.  Looking at the code:  
  
  https://github.com/ldionne/hana/blob/master/include/boost/hana/optional.hpp#L105  
  
the reader sees that Optional is related to concepts:  
  
  Comparable, Orderable, and Monad  
  
via the operators::of template; however, there's no mention  
of Applicative.  So the reader wonders further how the  
compiler knows that one of those Concepts is a "derived"  
Concept of Applicative.  Looking at the docs for the related  
concepts one finds:  
  
  The Monad concept represents Applicatives   
  
here:  
  
  http://ldionne.com/hana/structboost_1_1hana_1_1Monad.html  
  
hence, the reader guesses that maybe the Monad source would  
somehow mention Applicative; however, that source:  
  
  https://github.com/ldionne/hana/blob/master/include/boost/hana/monad.hpp  
  https://github.com/ldionne/hana/blob/master/include/boost/hana/fwd/monad.hpp  
  
contains no mention of Applicative in the code, but only in  
the comments.  So, how does the compiler know that Optional  
models Applicative?  
  
The extending documentation mentioned above makes no mention  
of the operators::of template.  So is this not needed when  
extending the library?  
  
The above narrative is what I have gone through trying to  
understand how to extend the library.  It shows I had to do  
a fair amount of guessing.  It would really help if the  
extending documentation would be more explicit about what  
was required to extend the library.  To be more specific,  
I'd appreciate it if the extending documentation were be  
modified so that everywhere I'm guessing in the above  
narrative, the documentation were clarified so that I'm no  
longer guessing or wondering how something is done.  
  
-regards,  
Larry

---

## Comment 1

> Username: cppljevans  
> Created at: 2015-06-23 14:28:52 UTC  
> Url: https://github.com/boostorg/hana/issues/155#issuecomment-114524282  

OOPS.  After reading a little further, I see that the public interface may be unrelated  
to any Concept.  At least the print public interface function doesn't seem to belong  
to any Concept.  
  
Sorry for noise.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-23 18:06:42 UTC  
> Url: https://github.com/boostorg/hana/issues/155#issuecomment-114593191  

> So, how does the compiler know that Optional  
> models Applicative?  
  
Just for the record, the compiler knows that `Optional` models `Applicative` because `Optional` implements `ap_impl` and `lift_impl`. Hence, when you write `models<Applicative, Optional>`, the compiler checks whether `ap_impl<Optional>` and `lift_impl<Optional>` are defined, which is indeed the case.
