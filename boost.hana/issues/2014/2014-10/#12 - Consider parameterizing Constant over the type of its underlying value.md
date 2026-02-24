# #12 - Consider parameterizing Constant over the type of its underlying value [Closed]

> Username: ldionne  
> Created at: 2014-10-05 00:39:03 UTC  
> Updated at: 2015-04-12 22:56:36 UTC  
> Closed at: 2015-04-12 22:56:36 UTC  
> Url: https://github.com/boostorg/hana/issues/12  

Would that interact well with the rest of the library? Would that be useful? This brings out the more general question of how to deal with parameterized type classes, which is a can of worms. See [this](http://permalink.gmane.org/gmane.comp.lib.boost.devel/254708) for some more comments on parameterized type classes.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-04-12 22:56:36 UTC  
> Url: https://github.com/boostorg/hana/issues/12#issuecomment-92147343  

The `Constant` concept is now parameterized, both in the documentation and in practice, via the `C::value_type` alias. This is not a super elegant solution IMO, but it does the job. Closing this.
