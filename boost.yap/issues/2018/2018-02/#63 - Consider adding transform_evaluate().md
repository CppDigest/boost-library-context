# #63 - Consider adding transform_evaluate() [Closed]

> Username: tzlaine  
> Created at: 2018-02-21 17:11:08 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-02-23 16:48:34 UTC  
> Url: https://github.com/boostorg/yap/issues/63  

> What does evaluate_with_context now do?  Let's say expr is "a + b".  Does  
> the context only apply to the evaluation of a and b as terminals, or does  
> it apply to the plus operation as well?  
  
  It applies to the plus operation first.  If the  
context has a handler for plus, then it's up to  
the context to handle recursion.  If it does not,  
then it becomes  
evaluate_with_context(a, ctx, x...)  
  + evaluate_with_context(b, ctx, x...)  
  
>  Are such applications of the  
> context conditional?  How does the reader quickly grasp what the  
> evaluate_with_context() call does?  This seems like really muddy code to  
> me.  If you have something else in mind, please provide more detail -- I  
> may of course be misunderstanding you.  
>  
  
  My idea is that it would behave exactly like  
transform, except that the default behavior for  
nodes that are not handled by the context is  
to evaluate the operators instead of building  
a new expression.  
  
Ah, I think I get it now.  This is really a transform_evaluate() then?  If so, that does sound useful.

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-02-23 16:48:33 UTC  
> Url: https://github.com/boostorg/yap/issues/63#issuecomment-368066489  

Mooted by recent API changes.

---

## Comment 2

> Username: ldionne  
> Created at: 2018-03-01 02:56:51 UTC  
> Url: https://github.com/boostorg/yap/issues/63#issuecomment-369458287  

Can you explain exactly how the functionality of `transform_evaluate` can now be achieved? Looked at the new documentation but can't figure it out easily.

---

## Comment 3

> Username: tzlaine  
> Created at: 2018-03-01 04:18:25 UTC  
> Url: https://github.com/boostorg/yap/issues/63#issuecomment-369469441  

Sure.  transform_evaluate(xform) is the same as transform(xform, evaluation()), now that multiple transform objects can be passed to transform().

---

## Comment 4

> Username: ldionne  
> Created at: 2018-03-01 04:27:56 UTC  
> Url: https://github.com/boostorg/yap/issues/63#issuecomment-369470618  

Ahhhh, I see.
