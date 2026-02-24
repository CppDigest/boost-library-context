# #104 - [Documentation] Why it is more important to document the concrete models than the subclasses of a Concept? [Closed]

> Username: viboes  
> Created at: 2015-06-13 14:00:06 UTC  
> Updated at: 2015-06-17 01:59:01 UTC  
> Closed at: 2015-06-17 01:59:01 UTC  
> Url: https://github.com/boostorg/hana/issues/104  

When describing a Concept you describe the Superclasses and the Concrete models of this concept. I appreciate both. We don't see the Subclasses. Why it is more important to document the concrete models than the subclasses?   
  
See e.g. http://ldionne.com/hana/structboost_1_1hana_1_1Applicative.html

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 20:02:38 UTC  
> Url: https://github.com/boostorg/hana/issues/104#issuecomment-111743799  

That's a good question. I guess that's just the way it has always been done in Concept-based libraries. For example, see [Fusion](http://www.boost.org/doc/libs/1_58_0/libs/fusion/doc/html/fusion/sequence/concepts/bidirectional_sequence.html); it only states what concept a concept is a "refinement of", but not the reverse relation. I'd say knowing the subclasses is slightly less useful than knowing the superclasses, but I'm not feeling strongly against or for this. I'll ask the question on the mailing list, just to see what people think of it.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-13 20:25:33 UTC  
> Url: https://github.com/boostorg/hana/issues/104#issuecomment-111747850  

Asked the question on the Boost.Devel mailing list, [here](http://thread.gmane.org/gmane.comp.lib.boost.devel/261165).

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-14 11:54:10 UTC  
> Url: https://github.com/boostorg/hana/issues/104#issuecomment-111816105  

I'm saying this because Haskell doesn't make any differences between instances and super classes.   
  
```  
Functor F => Applicative F  
```  
  
As the relationships between the type classes that some types must satisfy, I find the new superclass term confusing and not scalable. E.g. how would you express these relationships  
  
```  
Monoid a => Monoid (Maybe a)  
(Monoid a, Monoid b) => Monoid (a, b)  
Monoid a => Monoid (Dual a)  
```

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-14 12:22:28 UTC  
> Url: https://github.com/boostorg/hana/issues/104#issuecomment-111821782  

I agree with you about superclasses being the wrong term, as was fixed by #102. If we're not using the term "superclass" anymore (and we're not anymore on develop), do you see a reason of keeping this one open? I could add the "subclasses" in a _see also_ section of type classes, as proposed [here](http://article.gmane.org/gmane.comp.lib.boost.devel/261171). Would that be satisfying?

---

## Comment 5

> Username: viboes  
> Created at: 2015-06-14 14:36:55 UTC  
> Url: https://github.com/boostorg/hana/issues/104#issuecomment-111835613  

Louis, be free to close any issue I've created. My intention is just to show you that there is some alternatives. Up to you to make the choice.

---

## Comment 6

> Username: ldionne  
> Created at: 2015-06-14 15:14:56 UTC  
> Url: https://github.com/boostorg/hana/issues/104#issuecomment-111840676  

I understand. My goal is not to close as many issues as possible, but rather to find an appropriate resolution for each of them. I'll let this sleep for a while and see what else may come up on the mailing list, and then I'll see what I do.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-17 01:59:01 UTC  
> Url: https://github.com/boostorg/hana/issues/104#issuecomment-112622438  

Closing this in favour of #129.
