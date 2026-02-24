# #83 - Overwriting existing on_error values [Closed]

> Username: Lachlan-Frawley  
> Created at: 2025-09-19 09:04:45 UTC  
> Updated at: 2025-09-21 20:30:57 UTC  
> Closed at: 2025-09-21 20:30:57 UTC  
> Url: https://github.com/boostorg/leaf/issues/83  

The short of it is that I'd like to be able to do something like this:  
```  
auto load = leaf::on_error( e_active_object { "obj1"} );  
BOOST_LEAF_CHECK(obj1->do_something());  
  
load = leaf::on_error( e_active_object { "obj2"} );  
BOOST_LEAF_CHECK(obj2->do_something());  
```  
  
But this doesn't work, and I'm forced to do something like this:  
```  
{  
    auto load = leaf::on_error( e_active_object { "obj1"} );  
    BOOST_LEAF_CHECK(obj1->do_something());  
}  
  
{  
    auto load = leaf::on_error( e_active_object { "obj2"} );  
    BOOST_LEAF_CHECK(obj2->do_something());  
}  
```  
Which saddens me because it makes the code much less readable, and ruins the great ergonomics I've found using the library so far.  
  
As for the larger reason why I'd like to know if it's possible, I've been using LEAF in a project and have written an endurance test program which just runs until it breaks. Inside this program I have a bunch of identical objects doing the same operations with slightly different initialization parameters to see if a failure occurs. The above example roughly shows what I've been doing to accomplish this.  
  
Is this possible currently? Or is there any interest in adding such functionality to the library?  
  
Thanks.

---

## Comment 1

> Username: zajo  
> Created at: 2025-09-19 17:27:24 UTC  
> Updated at: 2025-09-19 17:34:30 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3313091892  

I presume you are using exceptions right? I'll look at this in the weekend, but what happens if in your first snippet you don't reuse `load`?  
```  
auto load1 = leaf::on_error( e_active_object { "obj1"} );  
obj1->do_something();  
  
auto load2 = leaf::on_error( e_active_object { "obj2"} );  
obj2->do_something();  
```  
  
I should have disabled the assignment operator.

---

## Comment 2

> Username: Lachlan-Frawley  
> Created at: 2025-09-19 23:28:34 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3314173160  

Oh no sorry, not using exceptions, I forgot the `BOOST_LEAF_CHECK` around them, I'll edit the issue to fix that.

---

## Comment 3

> Username: zajo  
> Created at: 2025-09-19 23:42:15 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3314204143  

OK cool. What about my other question, did you try not reusing the `load` object?

---

## Comment 4

> Username: Lachlan-Frawley  
> Created at: 2025-09-19 23:50:51 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3314229363  

Not reusing the load object like in your suggestion does not work, it still shows "obj1" instead of "obj2".

---

## Comment 5

> Username: Lachlan-Frawley  
> Created at: 2025-09-20 03:22:59 UTC  
> Updated at: 2025-09-20 03:23:15 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3314470591  

I was taking another look at my test code and realized I'd made a mistake with the order of returning an error to test this. After fixing it, yes "obj2" was showing up before "obj1".

---

## Comment 6

> Username: zajo  
> Created at: 2025-09-20 17:52:29 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3315138781  

OK, so, all good? For clarity, the logic with on_error is that it will check if an error object of the type has already been communicated for the current failure, and it will not overwrite something that's already there.

---

## Comment 7

> Username: Lachlan-Frawley  
> Created at: 2025-09-21 10:59:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3315919213  

I guess this approach works, but I'd definitely rather be able to assign over the top of existing `preloaded` objects. It'd make it much easier to use compared to writing out new variables to overwrite old ones, and would also more clearly communicate the intent behind such an action.  
  
As I see it, there are 2 ways of doing this:  
1. Add a move assignment operator to allow this:  
```  
auto load = leaf::on_error( e_active_object { "obj1" } );  
BOOST_LEAF_CHECK(obj1->do_something());  
  
load = leaf::on_error( e_active_object { "obj2" } );  
BOOST_LEAF_CHECK(obj2->do_something());  
```  
  
2. Add a helper function to replace the contents of a `preloaded` object  
```  
auto load = leaf::on_error( e_active_object { "obj1" } );  
BOOST_LEAF_CHECK(obj1->do_something());  
  
load.replace( e_active_object { "obj2" } );  
BOOST_LEAF_CHECK(obj2->do_something());  
```  
  
I could look at what changes might need to be made for this if you think this a worthwhile change?

---

## Comment 8

> Username: zajo  
> Created at: 2025-09-21 16:40:32 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3316108387  

The behavior is this:  
  
- on_error won’t overwrite something already there.  
- loading explicitly will always overwrite.  
  
I don’t want on_error to mess up deliberate error reporting. For example this could lead to different error info values depending on the call context.  
  
Perhaps in your use case you can pass a lambda to on_error and overwrite things there.

---

## Comment 9

> Username: Lachlan-Frawley  
> Created at: 2025-09-21 20:30:57 UTC  
> Url: https://github.com/boostorg/leaf/issues/83#issuecomment-3316245339  

That seems like a good idea, I'll give lambdas a try. Thanks.
