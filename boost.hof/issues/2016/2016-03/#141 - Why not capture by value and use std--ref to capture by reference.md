# #141 - [capture] Why not capture by value and use std::ref to capture by reference? [Closed]

> Username: ldionne  
> Created at: 2016-03-11 21:19:30 UTC  
> Updated at: 2016-11-11 00:35:27 UTC  
> Closed at: 2016-11-11 00:35:27 UTC  
> Url: https://github.com/boostorg/hof/issues/141  

This issue has been raised before on the mailing list, but I did not find a GitHub issue for it, so here we go. Basically, the documentation presents three distinct versions of `capture_*`:  
  
> ``` c++  
> // Capture lvalues by reference and rvalues by value.  
> template<class... Ts>  
> constexpr auto capture(Ts&&... xs);  
>   
> // Capture lvalues by reference and rvalue reference by reference  
> template<class... Ts>  
> constexpr auto capture_forward(Ts&&... xs);  
>   
> // Capture by decaying each value  
> template<class... Ts>  
> constexpr auto capture_decay(Ts&&... xs);  
> ```  
  
The question that immediately arises is why provide three functions instead of providing only one `capture` function that would capture everything by value, and then let the user capture stuff by reference with `std::ref`? This is what's done everywhere else in the library, and it seems both more consistent and perhaps easier to use as well.  
  
If this proposition is not acceptable, then the rationale should at least be documented.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-03-11 21:25:27 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195561240  

Note that the same comment applies to `fit::pack`.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-03-11 22:21:22 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195576139  

> The question that immediately arises is why provide three functions instead of providing only one capture function that would capture everything by value, and then let the user capture stuff by reference with std::ref?  
  
You can't do perfect capturing or capture with rvalue references.  
  
> This is what's done everywhere else in the library, and it seems both more consistent and perhaps easier to use as well.  
  
Yes, the rest of the library does it this way to keep it simple. However, when the user needs perfect capturing or capturing with an rvalue references then the overloads for `capture` can be used.  
  
Also, there is no way to do perfect capturing with lambdas in C++ either, so `fit::capture` is useful for that as well.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-03-11 22:29:13 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195582716  

> You can't do perfect capturing or capture with rvalue references.  
  
Ok, this is what I thought. Would it be possible to do it with some kind of `std::rvalue_ref` function? I know changing the STL is not the way to go for Fit, but I would still like the inconsistency between `capture`, `pack`, and the rest of Fit to be resolved.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-03-11 22:30:52 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195584078  

Perhaps Fit could default to capturing by value (thus the current `capture_decay`), and then provide `capture_forward` to do perfect-capturing. By the way, what's the use case for the current `capture`?

---

## Comment 5

> Username: pfultz2  
> Created at: 2016-03-11 22:42:09 UTC  
> Updated at: 2016-03-11 22:43:15 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195588303  

> Would it be possible to do it with some kind of std::rvalue_ref function?  
  
That could be possible, you would also need some kind of `std::perfect_ref`. However, I would prefer not to have that much "magic" going on, when we are just basically just re-emulating the current type deduction in C++. So I would prefer to have something more straightforward.  
  
> I know changing the STL is not the way to go for Fit, but I would still like the inconsistency between capture, pack, and the rest of Fit to be resolved.  
  
In general, the "by decay" works well throughout the library, but when the user needs more control over capturing that is where the `capture` decorator can be used. Same applies for `pack` which is like `capture` but without the join.  
  
> By the way, what's the use case for the current capture?  
  
By capturing the rvalues by rvalues, it can extend the lifetime of temporaries, because rvalue reference do not extend the lifetime of a temporary. It provides a nice balance between performance(`capture_forward`) and safety(`capture_decay`).

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-03-11 23:22:07 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195600823  

Another idea, `fit::construct` deduces the type categories as is, and the projection can be used to change the type categories, for example:  
  
``` cpp  
// Construct tuple by decay  
auto make_tuple = construct<std::tuple>().by(decay);  
  
template<class T>  
T&& forward_ref(T&& x)  
{  
    return x;  
}  
  
// Construct tuple with references  
auto forward_as_tuple = construct<std::tuple>().by(FIT_LIFT(forward_ref));  
```  
  
Perhaps, `capture` and `pack` could use the same semantics:  
  
``` cpp  
// Capture elements by decaying  
auto f_decay = capture(xs...).by(decay)(f);  
  
// Capture elements by forwarding  
auto f_forward = capture(xs...).by(FIT_LIFT(forward_ref))(f);  
  
```  
  
Also, the other cases of capturing rvalues by rvalues can be handled as well. I like this approach, What do you think?

---

## Comment 7

> Username: ldionne  
> Created at: 2016-03-12 21:19:50 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195810539  

That seems like an interesting avenue, but where does the `.by` comes from? IMO, the important thing is that  
1. `capture(xs...)` captures by value by default, since that is consistent with the rest of the library. Otherwise, it seems like you're setting up users for surprises.  
2. `capture(std::ref(xs)...)` captures by reference, as we would expect because it works for `partial` & friends.  
  
As long as these two conditions are met, you can keep `capture_forward` and the the other one that captures lvalues by reference and rvalues by value (`capture_extend`?). As long as the behaviour of these two additional `captures` are documented, I'm OK with that. But the default `capture` should be consistent with the rest of the library, IMO.  
  
The same goes for `pack`, of course.

---

## Comment 8

> Username: pfultz2  
> Created at: 2016-03-12 22:02:17 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195816873  

> That seems like an interesting avenue, but where does the .by comes from?  
  
In `fit::construct`, I provide a `by` member to make it easier to define projections, so these are equivalent:  
  
``` cpp  
auto make_tuple = construct<std::tuple>().by(decay);  
auto make_tuple = by(decay, construct<std::tuple>());  
```  
  
So I was thinking of providing the same member function for `capture` and `pack`.  
  
> capture(xs...) captures by value by default, since that is consistent with the rest of the library. Otherwise, it seems like you're setting up users for surprises.  
  
Maybe this is better, but I was wanting to make it consistent with `construct`. It has only one overload, and it constructs the class without changing the type category. However, I can see how it can be surprising to users, so it would be better not to do this.  
  
> The same goes for pack, of course.  
  
And the same would apply to `construct`, so I would need two overloads for each one:  
- `capture` and `capture_forward`  
- `pack` and `pack_forward`  
- `construct` and `construct_forward`  
  
Although, I don't know like using the `_forward` for the other overload, since the semantics are slightly different than `forward_as_tuple`. Maybe something like `capture_only` or `capture_fresh` or `capture_plain`, I don't know.  
  
Another option is just to have one overload, and if the user wants to change the category then the member `by` could be used. However, that could be a little confusing:  
- `capture` - by value  
- `capture.by(decay)` - equivalent to `capture`  
- `capture.by(std::ref)` - captures by `reference_wrapper` not references. Confusing.  
  
Also, doing it this way, the user doesn't have the option to write `by(std::ref, capture)`(or rather it has slightly different semantics). I don't really like this as it seems confusing as well.

---

## Comment 9

> Username: pfultz2  
> Created at: 2016-03-12 22:06:18 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-195818106  

> Although, I don't know like using the _forward for the other overload, since the semantics are slightly different than forward_as_tuple. Maybe something like capture_only or capture_fresh or capture_plain, I don't know.  
  
Maybe it could be called `basic_capture`, since `capture` can be built with it.   
  
``` cpp  
auto capture = basic_capture.by(decay);  
```  
  
I actually like that. So the overloads would be this:  
- `capture` and `basic_capture`  
- `pack` and `basic_pack`  
- `construct` and `basic_construct`

---

## Comment 10

> Username: ldionne  
> Created at: 2016-03-13 17:31:58 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-196008439  

> In fit::construct, I provide a by member to make it easier to define projections, so these are equivalent:  
  
Is this documented? When I looked at `fit::construct`, I don't think I saw anything like that.  
  
> Although, I don't know like using the `_forward` for the other overload, since the semantics are slightly different than `forward_as_tuple`.  
  
How are they different?  
  
Also, with `basic_capture`, how would we do perfect forwarding? I.e. what's the `f` required for `basic_capture.by(f)` to be equivalent to `capture_forward`?

---

## Comment 11

> Username: pfultz2  
> Created at: 2016-03-13 18:07:01 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-196014418  

> Is this documented? When I looked at fit::construct, I don't think I saw anything like that.  
  
Oops, this hasn't been documented. I need to document that.  
  
> How are they different?  
  
Here is how the value categories are transformed with the currently named `capture`:  
- `T&` -> `T&`  
- `T` -> `T`  
- `T&&` -> `T`  
  
And with the current `capture_forward`(and this is how `forward_as_tuple` works as well):  
- `T&` -> `T&`  
- `T` -> `T&&`  
- `T&&` -> `T&&`  
  
`basic_capture` will work like this:  
- `T&` -> `T&`  
- `T` -> `T`  
- `T&&` -> `T&&`  
  
No change to the value category.  
  
> Also, with basic_capture, how would we do perfect forwarding? I.e. what's the f required for basic_capture.by(f) to be equivalent to capture_forward?  
  
Something like this:  
  
``` cpp  
auto f = [](auto&& x) -> decltype(x)&& { return x; }  
```

---

## Comment 12

> Username: ldionne  
> Created at: 2016-03-16 20:56:13 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-197545801  

I don't understand how you can write a `basic_capture` that can differentiate on whether it is being passed a temporary or a rvalue reference. In both cases, you'll have to bind to them using a `T&&`, no?  
  
Anyway, the only thing I'm concerned about is that `capture` should be consistent with the rest of the library, _by default_. And also the functionality provided by `capture_forward` is handy, so that should be kept in one way or the other. I'm not attached to anything else, so I think you should implement it however you think is best and I won't argue.

---

## Comment 13

> Username: pfultz2  
> Created at: 2016-03-17 01:51:46 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-197646111  

> I don't understand how you can write a basic_capture that can differentiate on whether it is being passed a temporary or a rvalue reference. In both cases, you'll have to bind to them using a T&&, no?  
  
You're right, the type deduction doesn't let you distinguish between the two. I thought it could be distinguished from the fact the `T` in `T&&` could either be `U` or `U&&`, but its actually never `U&&`.   
  
So it looks like three overloads will be needed:  
- `capture`  
- `capture_forward`  
- `capture_basic`  
  
Or maybe I should prefix them instead:  
- `capture`  
- `forward_capture`  
- `basic_capture`  
  
Which do you think is better?  
  
> Anyway, the only thing I'm concerned about is that capture should be consistent with the rest of the library, by default.  
  
I agree.  
  
> I'm not attached to anything else, so I think you should implement it however you think is best and I won't argue.  
  
Well I do appreciate your feedback, it has been very helpful.

---

## Comment 14

> Username: ldionne  
> Created at: 2016-03-17 13:06:10 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-197870981  

> So it looks like three overloads will be needed:  
> [...]  
> Or maybe I should prefix them instead:  
> [...]  
> Which do you think is better?  
  
I personally prefer the `capture_xxx` variants. They will also be easier to find in the documentation if they share the same prefix than if they share the same suffix.  
  
> Well I do appreciate your feedback, it has been very helpful.  
  
I'm glad you find it so!

---

## Comment 15

> Username: pfultz2  
> Created at: 2016-11-04 17:54:31 UTC  
> Url: https://github.com/boostorg/hof/issues/141#issuecomment-258502955  

So as of commit [5efe6c47b3a6354f4b044db0f06e7cb5f496e077](https://github.com/pfultz2/Fit/commit/5efe6c47b3a6354f4b044db0f06e7cb5f496e077) this has been implemeted.
