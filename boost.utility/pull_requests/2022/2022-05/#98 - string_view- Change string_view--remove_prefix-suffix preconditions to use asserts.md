# #98 string_view: Change string_view::remove_prefix/suffix preconditions to use asserts [Merged]

> Username: Lastique  
> Created at: 2022-05-02 22:09:05 UTC  
> Updated at: 2022-07-06 00:14:49 UTC  
> Merged at: 2022-07-06 00:14:43 UTC  
> Closed at: 2022-07-06 00:14:44 UTC  
> Url: https://github.com/boostorg/utility/pull/98  

This is in line with `std::string_view::remove_prefix`/`suffix` definition.  
  
Closes https://github.com/boostorg/utility/issues/92.  
  
@mclow Please take a look.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-07-05 11:03:19 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1174929911  

@mclow Ping?

---

## Comment 2

> Username: mclow  
> Created_at: 2022-07-05 12:58:39 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1175030730  

Please read the discussion in #92.   The reason the @sehe asked for this change is that he wants "consistent undefined behavior" across different implementations.   While I have no objections to adding an ASSERT, I am quite opposed to that goal.

---

## Comment 3

> Username: sehe  
> Created_at: 2022-07-05 13:16:36 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1175048776  

Sigh @mclow   
  
I answered that question with "yes" (because honesty is a virtue), but there is really no reason to dismiss the [_rest_ of my response](https://github.com/boostorg/utility/issues/92#issuecomment-1141512797), instead reducing it to the (loaded) question frame:  
  
> > Maybe a better comment is "You want consistent undefined behavior?"  
>   
> Yes. Because I want to avoid the silent pitfall where people rely on this lenient behavior of a supposed standard-compliant string_view only to get spurious UB when they finally do move to std::string_view (or some other implementation).  
>  
> [Hyrum's Law](https://www.hyrumslaw.com/) with a twist  
  
So if you're going to present my opinion as argument, I'd appreciate you don't misrepresent it as your preconceived frame.  
  
It's dangerous to be more lenient than the standardized interface that you're substituting for. It sets your users up for failure when they switch or mix string_view implementations.  
  
Perhaps you should try replacing the "consistency of UB" frame with "prefer loud failure over UB", even if the UB happens not with your particular implementation. You can of course righteously claim your implementation is superior but IMO that's denying the reality of use-cases for this implementation. Your implementation (only) makes sense in the existing ecosystem, and should work well in that context.  
  
I tried to explain this rationale [a month ago](https://github.com/boostorg/utility/issues/92#issuecomment-1141523186), but you didn't respond.  
  
I'll leave any decision to you. I'm just here looking out for Boost users. It's your library after all, feel free to ignore.

---

## Review 4 [Commented]

> Username: sehe  
> Created_at: 2022-07-05 13:24:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/98#pullrequestreview-1028630545  

📁 include/boost/utility/string_view.hpp

```diff
 143 |         BOOST_CXX14_CONSTEXPR void remove_prefix(size_type n) {
 143 |-             if ( n > len_ )
 144 |-                 n = len_;
```

> Username: sehe  
> Created_at: 2022-07-05 13:24:17 UTC  
> Updated_at: 2022-07-05 13:25:29 UTC  
> Url: https://github.com/boostorg/utility/pull/98#discussion_r913794243  

The "clamp" doesn't really need to go, right. Assuming responsible users also compile /test with BOOST_ASSERT enabled they will learn that their use of the member function is not portable and risks UB (on other implementation).  
  
Perhaps this (just assing the assert) is a reasonable mid-point @mclow as otherwise one might even argue that this change might break existing users of Boost Utility.


---

## Comment 5

> Username: Lastique  
> Created_at: 2022-07-05 20:47:41 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1175482902  

> Please read the discussion in #92. The reason the @sehe asked for this change is that he wants "consistent undefined behavior" across different implementations. While I have no objections to adding an ASSERT, I am quite opposed to that goal.  
  
I did read it, and it seems to me each of you two put a different meaning into "consistent undefined behavior". What I think @sehe is saying (and I agree with) is "let the call be UB if the argument is invalid - this would be *consistent* with other implementations of `string_view`". That is, the UB is consistent across implementations. You're saying there's no such thing as "consistent UB", and this is true by definition of UB, but this is not what @sehe argues for.  
  
BTW, @sehe, you could use a less aggressive tone and explain your motivation more clearly. I understand it can be frustrating when someone disagrees with you or doesn't understand what you're saying, but we're all trying to make Boost better, so please be more patient and calm. Yes, it will take more effort, but the result will likely be better (that is, `string_view` will hopefully improve).  
  
In any case, since @mclow doesn't mind against adding an assert, and removing the check may indeed cause a regression in the user's code, I will keep the check and add the assert. This will be a temporary measure so that eventually the check can be removed.

---

## Comment 6

> Username: sehe  
> Created_at: 2022-07-05 21:02:37 UTC  
> Updated_at: 2022-07-05 21:04:44 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1175495656  

@Lastique You're probably right.  I had already concluded - offline - that I should work on my skills to effectively communicate my point. I find that pretty hard anyways, and I did sense a form of dismissiveness which I rarely have a will to argue against. But you're right, we're all trying to improve boost.  
  
Your summary is very concise. I'm happy with the current change which does indeed seem to be the sweet spot. Even people looking at the source will see the contract clearly.  
  
Thanks @mclow too, and I will bring more patience to the game next time, cheers

---

## Comment 7

> Username: mclow  
> Created_at: 2022-07-05 21:15:48 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1175505942  

I'll try again; maybe I was not being clear:  
  
@Lastique wrote:  
> I did read it, and it seems to me each of you two put a different meaning into "consistent undefined behavior". What I think @sehe is saying (and I agree with) is "let the call be UB if the argument is invalid - this would be consistent with other implementations of string_view".   
  
The call is UB. We could document that better - and I will do so.  
  
What _I think_ that @sehe is asking for is that *in the case of UB*, he wants `boost::string_view` to behave the same as (say) MSVC's implementation of `string_view`.  That's what I'm objecting to.  In an ideal world (ok, in an ideal world, there would be no UB, but ...) calls to `string_view::remove_prefix` that didn't satisfy the preconditions would not be idempotent. That is, they would return a different result on every single call.  But I don't think that's worth doing (too much overhead)

---

## Comment 8

> Username: Lastique  
> Created_at: 2022-07-05 21:27:32 UTC  
> Updated_at: 2022-07-05 21:28:16 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1175514989  

> What _I think_ that @sehe is asking for is that _in the case of UB_, he wants `boost::string_view` to behave the same as (say) MSVC's implementation of `string_view`. That's what I'm objecting to.  
  
Agreed, replicating UB across implementations is unrealistic and pointless. Though I did not get the impression that this was his intention.

---

## Comment 9

> Username: sehe  
> Created_at: 2022-07-05 22:38:29 UTC  
> Url: https://github.com/boostorg/utility/pull/98#issuecomment-1175570098  

> Though I did not get the impression that this was his intention.  
💯 I'm sad I wasn't able to make my point clearer. It's indeed about making the contract consistent with standard implementations.

---
