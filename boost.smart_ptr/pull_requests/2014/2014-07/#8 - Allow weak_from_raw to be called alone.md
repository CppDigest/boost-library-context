# #8 Allow weak_from_raw to be called alone [Closed]

> Username: uecasm  
> Created at: 2014-07-24 04:41:04 UTC  
> Updated at: 2015-10-12 05:23:07 UTC  
> Closed at: 2015-10-12 05:23:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8  

Currently weak_from_raw fails unless shared_from_raw has been called previously.  This doesn't seem like reasonable behaviour, especially when only a weak_ptr is desired and the overhead of creating a shared_ptr and degrading it back to a weak_ptr again seems silly.

---

## Comment 1

> Username: uecasm  
> Created_at: 2014-07-24 04:44:19 UTC  
> Updated_at: 2014-07-24 07:49:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-49967066  

Possible alternative fix would be to make a weak_from_this member similar to the existing shared_from_this, and make weak_from_raw call that instead of directly accessing weak_this_.  (I actually think this would be better style but I wanted to make this a very minimal change in the hope of getting it into 1.56.)  
  
Note that I have tested this change and it behaves as expected, although I suspect it will break the enable_shared_from_raw unit test, since that appears to expect the prior behaviour.  (I'm not set up to run the tests myself so I can't verify.)

---

## Comment 2

> Username: pdimov  
> Created_at: 2014-07-30 15:18:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-50629467  

I'm not sure what you are trying to fix. enable_shared_from_raw_test doesn't call shared_from_raw:  
  
```  
    X *p(new X);  
    boost::weak_ptr<X> weak = boost::weak_from_raw(p);  
    BOOST_TEST(weak.expired());  
    boost::shared_ptr<X> shared(p);  
    weak = boost::weak_from_raw(p);  
    BOOST_TEST(weak.expired() == false);  
    boost::shared_ptr<X> shared2(weak);  
    BOOST_TEST((shared < shared2 || shared2 < shared) == false);  
    BOOST_TEST(shared.get() == p);  
```

---

## Comment 3

> Username: uecasm  
> Created_at: 2014-07-31 00:19:20 UTC  
> Updated_at: 2014-07-31 00:37:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-50698478  

What I'm trying to fix is that the _second_ call to weak_from_raw should not be required.  If weak_from_raw is called in the constructor of X to obtain a weak_ptr to itself (or prior to the creation of the "real" shared_ptr as in the test above), this should **not** be expired and it should be able to hand these out to its child objects without fear.  When the "real" shared_ptr is constructed this "takes over" ownership from the internal shared_ptr (and if this fails to occur then there will be a memory leak but otherwise nothing harmful).  All of this is already true if shared_from_raw is called instead of weak_from_raw; I do not think they should behave differently from each other.  
  
(I've even seen an older ML post where someone encountered this same issue and it was suggested to call shared_from_raw and discard the result prior to calling weak_from_raw.  Which does work, but is obviously silly, and part of the motivation for this change.)  
  
Further discussion and rationale here: http://lists.boost.org/boost-users/2014/07/82537.php

---

## Comment 4

> Username: uecasm  
> Created_at: 2014-07-31 00:28:29 UTC  
> Updated_at: 2014-07-31 00:33:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-50699172  

Or, to rephrase it in terms of that test; instead of the above, I want _this_ test to pass:  
  
```  
X *p(new X);  
boost::weak_ptr<X> weak = boost::weak_from_raw(p);  
BOOST_TEST(weak.expired() == false);  
{  
    boost::shared_ptr<X> shared(p);  
    BOOST_TEST(weak.expired() == false);  
    boost::shared_ptr<X> shared2(weak);  
    BOOST_TEST((shared < shared2 || shared2 < shared) == false);  
    BOOST_TEST(shared.get() == p);  
}  
BOOST_TEST(weak.expired());  
```  
  
If you think there should be a way to detect whether that external pointer has been created yet or not (I can't see anyone using it in normal code, but it may be useful when debugging) it would be easy to do so; as I said above it would be trivial to add a method that indicated this by checking `shared_this_.empty()` (if it is not empty, then you are in the "leak zone" after {shared,weak}_from_raw has been called but before the external pointer has been created).  
  
I restricted this to a one-liner because I wanted to keep it minimal.  If you prefer, I could submit a more complete patch (either separately or altering this one) that makes it more obvious that shared_from_raw and weak_from_raw share the same behaviour.

---

## Comment 5

> Username: pdimov  
> Created_at: 2014-07-31 10:19:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-50741558  

> (I've even seen an older ML post where someone encountered this same issue and it was suggested to call shared_from_raw and discard the result prior to calling weak_from_raw. Which does work, but is obviously silly, and part of the motivation for this change.)  
  
That is indeed silly. But the fix doesn't quite strike me as correct. Putting aside the current behavior for a moment, what I think ought to happen is this:  
  
```  
X * p( new X );  
boost::weak_ptr<X> weak = boost::weak_from_raw( p );  
BOOST_TEST( weak.expired() ); // no shared_ptr yet  
// shared_from_raw( p ) would throw here  
  
boost::shared_ptr<X> shared(p);  
BOOST_TEST( !weak.expired() );  
  
boost::shared_ptr<X> shared2(weak);  
BOOST_TEST((shared < shared2 || shared2 < shared) == false);  
BOOST_TEST(shared.get() == p);  
```  
  
and in fact, `enable_shared_from_this` needs to have `weak_from_this` that works in the same way - this behavior is backward-compatible.  
  
Unfortunately, I don't have the time at the moment to see how this can be implemented.

---

## Comment 6

> Username: uecasm  
> Created_at: 2014-08-03 23:24:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51006649  

I can see what you're saying, but that's expressly what I **don't** want it to do.  Part of the "advantage" of _from_raw over its shared_from_this sibling is that _from_raw can be called from the shared class's own constructor, which lets you set up weak callbacks and child references without having to go through the factory-method shenanigans that shared_from_this requires, and without having to create a shared_ptr only to degrade it back to a weak_ptr again.  
  
As I said, if you want to be able to simply detect the case where the object hasn't yet become externally owned, that's not hard to do (I'm happy to write a patch for that if you want), but having the pointer be expired is not the way to do it, in my view.  
  
And as I stressed in one of my followup ML posts, as far as I am aware such usage is completely safe -- the worst that can happen is a memory leak from the internal-self-referential shared_ptr if you fail to create the "external" shared_ptr, and even that is no worse than setting up any other shared_ptr cyclic reference.  There is never any risk of UB.

---

## Comment 7

> Username: pdimov  
> Created_at: 2014-08-04 01:53:58 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51010946  

If things worked as I say, you would still be able to call `weak_from_raw` from the constructor and set up weak callbacks and references (just not `shared_from_raw`).

---

## Comment 8

> Username: uecasm  
> Created_at: 2014-08-05 09:13:53 UTC  
> Updated_at: 2014-08-05 09:16:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51171046  

Again, the difference between shared_from_this and {shared,weak}_from_raw is that the latter actually does have a real shared_ptr in existence keeping the refcounts sane and the object alive prior to external assignment, while the former does not.  So there is no need to have the weak_ptr be expired or the shared_ptr be non-constructible.  
  
Additionally, it seems like a violation of weak_ptr's usage contract to have an apparently-expired pointer be resurrected at a later time.  It's not uncommon to have something that receives a weak_ptr to check if it is expired (either at use or periodically) and discard it if so, not expecting that it could ever un-expire.  
  
That's why I suggested the behaviour that I did.  It reflects the internal reality of _from_raw's implementation (that there really is a valid shared_ptr around, albeit a self-referential one), and avoids violating expectations of weak_ptr's contract, while still improving consistency.  
  
---  
  
I'm guessing it's too late to make the release now anyway, so maybe I should just update this to the more "complete" version?  Maybe this might be clearer that way.

---

## Comment 9

> Username: pdimov  
> Created_at: 2014-08-06 10:10:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51315669  

> And as I stressed in one of my followup ML posts, as far as I am aware such usage is completely safe -- the worst that can happen is a memory leak from the internal-self-referential shared_ptr if you fail to create the "external" shared_ptr,  
  
I'm not sure I see where the memory leak comes from.  
  
The worst that can happen is not a leak, it's a dangling `shared_ptr`, but it seems that `~enable_shared_from_raw` guards against this with a `BOOST_ASSERT`.  
  
I'll look into this some more, as it seems that we've all forgotten how things were supposed to work.

---

## Comment 10

> Username: pdimov  
> Created_at: 2014-08-06 19:47:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51386875  

There's one interesting use case that your fix breaks (and, ironically, fixes from the opposite side). Consider this test code:  
  
```  
#include <boost/smart_ptr/enable_shared_from_raw.hpp>  
#include <boost/weak_ptr.hpp>  
#include <boost/core/lightweight_test.hpp>  
  
class X;  
  
static boost::weak_ptr< X > r_;  
  
void register_( boost::weak_ptr< X > const & r )  
{  
    r_ = r;  
}  
  
void check_( boost::weak_ptr< X > const & r )  
{  
    BOOST_TEST( !( r < r_ ) && !( r_ < r ) );  
}  
  
void unregister_( boost::weak_ptr< X > const & r )  
{  
    BOOST_TEST( !( r < r_ ) && !( r_ < r ) );  
    r_.reset();  
}  
  
class X: public boost::enable_shared_from_raw  
{  
public:  
  
    X()  
    {  
        register_( boost::shared_from_raw( this ) );  
    }  
  
    ~X()  
    {  
        unregister_( boost::weak_from_raw( this ) );  
    }  
  
    void check()  
    {  
        check_( boost::weak_from_raw( this ) );  
    }  
};  
  
int main()  
{  
    {  
        boost::shared_ptr< X > px( new X );  
        px->check();  
    }  
  
    return boost::report_errors();  
}  
```  
  
The point is that the constructor registers the object somewhere, and the destructor unregisters it, and the whole enterprise depends on the `weak_ptr` instances obtained in the constructor, during the lifetime, and in the destructor, sharing ownership (so that the registry can figure out what object is being unregistered).  
  
Now, you can see that I'm using `shared_from_raw` in the constructor, because without your patch it doesn't return the right `weak_ptr`. After your patch, we can use `weak_from_raw` in the constructor as well, and that's how it must be.  
  
However, before your patch, `weak_from_raw` in the destructor returns the "right" (expired) `weak_ptr`, and after, it no longer does, as it reinitializes.  
  
This indeed matches the behavior of `shared_from_raw`, which reinitializes if called from the destructor as well, instead of throwing. I suspect, however, that this is not the behavior we want, because the use case in the test is relatively common and sensible.

---

## Comment 11

> Username: uecasm  
> Created_at: 2014-08-08 00:25:21 UTC  
> Updated_at: 2014-08-08 00:28:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51550174  

Maybe it's not the right terminology, but I consider a cyclic shared_ptr as a memory leak, as it represents an indestructible object that is no longer accessible from "outside".  *_from_raw intentionally sets up such a cycle temporarily; this is "repaired" once the external shared_ptr is created but until then it's a potentially leaked object.  If the external pointer is never created then it's an actually leaked object -- even though there's still a valid shared_ptr pointing at it, this is inaccessible.  (Granted there are some esoteric ways this could be recovered from but that would require very weird code design, and certainly not intended use, so I don't think it's worthy of consideration.)  
  
Your second point about the weak unregistration, I suppose that's true.  Having said that I'm not sure that code like the above makes sense to begin with -- of necessity, the weak_ptr will always be expired and can't sensibly be unregistered -- the registry would presumably be holding weak_ptrs (otherwise the destructor couldn't have been called) and hence wouldn't need unregistration, it would just remove any expired weak_ptrs.  I don't think it's sensible to call shared_from_raw from a destructor, just like it's not sensible to call shared_from_this there -- that extends to weak_from_raw as well, I think.  
  
Still, initing twice is definitely unintended, and it could be readily solved by making the init only happen once rather than basing it purely on the weak_ptr being expired; probably the simplest way to do that would be to check if the weak_ptr is empty rather than expired, although that's nonstandard.

---

## Comment 12

> Username: uecasm  
> Created_at: 2014-08-08 00:52:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51551744  

I've made that fix as discussed above, and updated the test case for the new behaviour, including verification that the weak_ptr is expired in the destructor and does not result in re-creation of the self-reference.  The test case passes on Boost 1.53 with these patches applied.

---

## Comment 13

> Username: uecasm  
> Created_at: 2014-08-08 01:03:16 UTC  
> Updated_at: 2014-08-08 01:05:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51552352  

Actually, looking at that `~enable_shared_from_raw` assertion, I'm not really sure what it's trying to do.  I cannot see any circumstance in which it will fire.  It certainly doesn't catch the case where the self-reference is left dangling -- it's impossible to detect that from the destructor.

---

## Comment 14

> Username: pdimov  
> Created_at: 2014-08-08 10:39:55 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51586665  

> Actually, looking at that  ~enable_shared_from_raw  assertion, I'm not really sure what it's trying to do. I cannot see any circumstance in which it will fire.  
  
This example should activate the assertion. It does for me.  
  
```  
#include <boost/smart_ptr/enable_shared_from_raw.hpp>  
#include <boost/shared_ptr.hpp>  
  
class X: public boost::enable_shared_from_raw  
{  
};  
  
int main()  
{  
    boost::shared_ptr<X> px;  
  
    {  
        X x;  
        px = boost::shared_from_raw( &x );  
    }  
}  
```

---

## Comment 15

> Username: pdimov  
> Created_at: 2014-08-08 18:37:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51641176  

> I've made that fix as discussed above, and updated the test case for the new behaviour, including verification that the weak_ptr is expired in the destructor and does not result in re-creation of the self-reference. The test case passes on Boost 1.53 with these patches applied.  
  
It breaks `esft_constructor_test.cpp` (which I've renamed to `shared_from_raw_test5.cpp`) though. Line 129 and below test reinitialization.

---

## Comment 16

> Username: uecasm  
> Created_at: 2014-08-11 00:43:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-51732914  

> It breaks  esft_constructor_test.cpp  (which I've renamed to  shared_from_raw_test5.cpp ) though. Line 129 and below test reinitialization.  
  
Ok, so all shared_ptr have been destroyed and it's creating a new shared_ptr from the same raw object for the second time.  This is definitely not sane code when using the default deleter, and while it can be sane with a custom deleter, I'm highly dubious that something like this would occur outside of a unit test.  Currently it's throwing a bad_weak_ptr exception when attempting this; in my mind that seems like reasonable behaviour.  
Would you prefer that I update this test to reflect the new behaviour or see if I can work out a way to preserve the old behaviour in this case?

---

## Comment 17

> Username: uecasm  
> Created_at: 2014-10-06 22:26:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-58109995  

Since 1.57 is approaching: ping.  (I notice there have been some changes to tests related to shared_from_raw/weak_from_raw; I haven't looked in detail but I think this issue remains.)

---

## Comment 18

> Username: uecasm  
> Created_at: 2015-01-28 06:24:47 UTC  
> Updated_at: 2015-01-28 06:39:51 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-71787206  

I haven't had a look at the rest of the code around the commit referenced above yet, but from looking just at that commit, can I suggest adding a `BOOST_TEST(r_.expired());` after the `shared_ptr` is destroyed in weak_from_raw_test4.cpp?  Removing the last shared_ptr to the object should destroy it, and the weak_ptr should be expired even if `unregister_` didn't get called.  (Maybe also check for `empty()` to prove that `unregister_` did get called?)  
  
Or maybe that should be a separate test case identical in all respects to weak_from_raw_test4.cpp except that `unregister_` has been removed -- the original test case should leave `r_` empty while the not-calling-unregister one should leave it non-empty but expired.  
  
And I'm still not convinced that shared_from_raw_test5.cpp's re-initialisation test isn't dodgy -- as the test itself asserts, the deleter is called twice on the same object.  That'd be fatal if it wasn't using a non-deleting deleter.

---

## Comment 19

> Username: pdimov  
> Created_at: 2015-01-28 10:47:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-71814381  

> And I'm still not convinced that shared_from_raw_test5.cpp's re-initialisation test isn't dodgy -- as the test itself asserts, the deleter is called twice on the same object. That'd be fatal if it wasn't using a non-deleting deleter.  
  
The actual use cases do not involve the same deleter. The second one is typically null or non-deleting, whereas the first one is. You pass a raw pointer to the object to some library, which temporarily creates a shared_ptr with a null deleter to it to do some unspecified library business. (I remember Boost.Python doing such things, for example.)

---

## Comment 20

> Username: pdimov  
> Created_at: 2015-01-28 10:58:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/8#issuecomment-71815782  

Added the new expiration test in 711c36958a56af392baf92ad3bac749b71bf76dd.

---
