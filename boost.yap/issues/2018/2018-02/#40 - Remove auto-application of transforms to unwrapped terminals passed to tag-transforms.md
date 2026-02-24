# #40 - Remove auto-application of transforms to unwrapped terminals passed to tag-transforms [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 00:33:34 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/40  

I think you're forgetting that the terminal transform is  
applied before calling operator()(xxx_tag, ...).  
Thus, the result is actually:  
  
make_expression<expr_kind::call>(checked_add{},  
  transform(transform(1_p, *this), *this),  
  transform(transform(2_p, *this), *this)).  
  
  Since the extra transform tacks on another call expr,  
we end up up coming right back around to operator()(call_tag).  
  
I was forgetting that!  Thanks.  
   
  The bottom line is, any transform which is not idempotent  
for terminals is badly broken if you unwrap terminals  
automatically.  
  
  If you don't apply the terminal transform, but still  
unwrap terminals, then it's *still* broken, because  
then the terminal transform gets skipped entirely.  
  
  Actually, the code I posted fails to compile (even after  
fixing the obvious typos) because the recursion makes  
it impossible for auto to deduce the return type.  
  
Right, and although this saves the user from runtime infinite recursion, it is a very obscure failure mode.  This more than outweighs the convenience of the current interface.  I'm convinced the auto-evaluating, and in fact all terminal unwrapping in tag-transforms, should be removed.  
  
...  
  
> Actually, I take this back, at least partially -- I think auto-unwrapping  
> in some form might still be useful without auto-applying the transform.  
> I'll need to experiment with that a bit. *TODO*  
>  
  
  If you can figure out a way to do it such that  
unwrapping is only applied for arguments that are  
*explicitly* intended to match terminals, then  
it would work.  If you imagine the semantics being  
as if unwrapping the terminal were an implicit  
conversion, then it would probably work correctly  
in all cases.  Unfortunately, I don't know how to  
implement this.  
  
I don't know if it needs to be so fancy.  I could just revert back to the old behavior, which always unwraps terminals, but does not auto-apply transforms.
