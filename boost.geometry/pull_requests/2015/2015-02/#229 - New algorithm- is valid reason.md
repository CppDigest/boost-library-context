# #229 New algorithm: is valid reason [Merged]

> Username: mkaravel  
> Created at: 2015-02-20 08:56:31 UTC  
> Updated at: 2015-02-25 13:19:08 UTC  
> Merged at: 2015-02-25 08:26:30 UTC  
> Closed at: 2015-02-25 08:26:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/229  

This PR proposes a new algorithm: `is_valid_reason`.  
The free function `is_valid_reason` takes a geometry and returns a string indicating is the geometry is valid or a message indicating the source of invalidity.  
  
Internally this has been implemented by introducing visitors in the `is_valid` algorithm: inside the `is_valid` algorithm, the visitor sets an appropriate failure type value, and possibly registers additional data related to the failure; it is then up to the visitor implementation to decide what to do with the data passed.  
  
Two visitors have been implemented as policies (stored in `boost/geometry/policies/is_valid`):  
- `is_valid_null_policy`: does nothing with the passed data (this policy is used by default in the `is_valid` algorithm)  
- `failing_reason_policy`: keeps track of the failure value and possibly the additional data, and outputs a relevant message to an `std::ostringstream`; this policy is used by the `is_valid_reason` algorithm (the message in the string stream is returned by the algorithm)  
  
Things to do and/or consider:  
- Is the enum type `validity_failure_type` at the appropriate location in BG's file tree?  
- The documentation for `is_valid_reason` is not there yet; will be added once the code is reviewed and merged.  
- To what level or detail should the failure messages dive into? For example for a polygon (invalid actually) where on interior ring is touching from the outside the exterior ring, the current version of the algorithm reports this case as an "invalid self-intersection point"; should the message rather be that the interior ring is outside the exterior ring (needs additional code to implement this feature).  
  
A final note on the policy design: the policy's visitor is designed to take the failure value as a template parameter rather than as an argument. This has been done on purpose in order to allow for the possibility to call the visitor's apply method that take data with different types of data (and whose types depends of course on the type of failure). So with this design, for example, if the failure value is `failure_self_intersections` all turns are passed, where as if the failure value is `failure_spikes` the apex of the spike is passed.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-02-21 13:35:56 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122209  

Should this not be "Geometry" ? Also a polygon can have intersection interiors

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-02-21 13:37:10 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122216  

What is the difference between holes outside boundary?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-02-21 13:38:33 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122218  

Do we have "... as open but is closed" too? Or is that no problem?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-02-21 13:39:39 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122223  

We should talk about either interiors (we have that everywhere) or holes (not in OGC spec) but not mixed.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-02-21 13:40:40 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122230  

If a polygon has holes that intersect, then we either have invalid self-intersection points or nested holes (see cases above).  
This refers to multi-polygons whose individual polygons are valid, but are such that their interiors intersect (in which case the multi-polygon is invalid, but not the individual polygons).  
This is why I restrict this to multi-polygons.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-02-21 13:43:29 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122245  

The holes-outside-boundary case refers to the case where an interior boundary is not inside the exterior boundary.  
This refers to the case were all holes are inside the (closure of the) exterior boundary, but the interior of the polygon is disconnected. If you look at the example in the documentation of `is_valid`, it refers to exactly this case.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-02-21 13:44:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75372211  

I like the design with visit_policy

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-02-21 13:44:52 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122252  

This can never happen. An open geometry is by default closed (the closing point is automatically added). If you define a geometry as open, but the two endpoints coincide, you simply have a duplicate point at the end.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-02-21 13:46:28 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122259  

A hole for me is the interior of an inner ring. The interior above refers to the interior of the areal geometry (so what is outside holes).  
This is what I had in mind at least. I will check again to make sure I am self-consistent.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-02-21 13:46:46 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122264  

So this is invalid (was already). I find the word "dimension" a little confusing - it is the topological dimension, no? Maybe just add "topological" here?

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-02-21 13:47:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75372328  

Should we also have a version of the `is_valid()` and/or `is_valid_reason()` free functions that take a visitor as a second argument?

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-02-21 13:49:20 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122280  

Yes, it is topological dimension.  
Will add it in both the enum type value as well as the message.  
Thanks for pointing this out.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2015-02-21 13:52:21 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122296  

I don't know if it is a good idea, but just brainstorm: we have this pattern often, visitor.template ... no_failure, return true, or visitor.template failure, return false. Can we not just return visitor.template apply<....> ? So then the apply with no-failure always returns true and the rest false? Or does this have drawbacks?  
Anyway, if good idea, you don't have to apply that now, we can postpone that action.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2015-02-21 13:56:48 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122325  

```  
#include <sstream>  
```

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2015-02-21 13:58:12 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122330  

Looks very good and very small, nice.  
sstr -> I would call it just "stream" that avoids the abbreviation

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-02-21 13:59:43 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122337  

What I can do it is to have the visitor's apply method return the boolean. If that is what you mean it is easy to do. I already considered doing so, but I decided to avoid it: the drawback that I see is that it "complicates" the interface of the visitor. I would prefer the visitor to just take info from the algorithm passively, rather than actively participate in computing the result. This way we can avoid potential "malicious" policies that disregard the failure value and return whatever....  
  
What I think (I have to double-check this, since I think I already tried it) I cannot do is to eliminate the `if (...)` statement. When I call the visitor I need the exact precise failure type at compile time.

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2015-02-21 14:00:27 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122339  

The inline is not needed in a cpp file. Anyway, no problem further

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-02-21 14:00:52 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122343  

Will add it. Thanks.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-02-21 14:01:05 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122345  

Sure. I will do this also.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-02-21 14:02:16 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122347  

In the past you had "complained" about it, so I decided to consistently use `inline` even in `.cpp` files.

---

## Comment 21

> Username: barendgehrels  
> Created_at: 2015-02-21 14:02:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75372908  

I like it. It is a large change-list but most changes follow the same pattern. It is complete with tests, and you mention docs will follow later. Thanks a lot, I'm OK with merging.

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2015-02-21 14:27:07 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122465  

;-) I hope I complained about it in a headerfile - there they belong...

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2015-02-21 14:30:19 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122485  

OK, but it can be an advantage too, to have a specific visitor that copies normal behaviour but for one specific situation it reverses the result...  
It is for me not necessary to avoid the if, I was just considering letting the visitor return the result indeed. If you considered this and still like it, OK for me.

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2015-02-21 14:32:55 UTC  
> Updated_at: 2015-02-21 14:33:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75374073  

You mean, as a sort of strategy? Good idea but I would postpone this a   
bit, let's first release this, get the visitor interface completely   
settled down, and then consider it again.  
(this was an answer on is_valid is_valid_reason with argument, it is now a bit out of flow)

---

## Comment 25

> Username: mkaravel  
> Created_at: 2015-02-21 14:37:08 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122532  

Done. See commits 9357d09, 667d5ea and 0987bc9.

---

## Comment 26

> Username: mkaravel  
> Created_at: 2015-02-21 14:38:08 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122540  

Done. See commit c098f89.

---

## Comment 27

> Username: mkaravel  
> Created_at: 2015-02-21 14:38:16 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122542  

Done. See commit c098f89.

---

## Comment 28

> Username: barendgehrels  
> Created_at: 2015-02-21 14:38:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75374297  

I don't know if I should mention this now, but it occurred to me that is_valid_reason is actually a weird name. I suggested it myself, plus it is in PostGIS  
http://postgis.net/docs/ST_IsValidReason.html  
so we could comply with that.  
However, it is not the reason for validity - it is the reason for invalidity... get_reason_for_invalidity... hmmm, I don't know. Maybe just leave it as it is

---

## Comment 29

> Username: mkaravel  
> Created_at: 2015-02-21 14:40:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75374344  

About `is_valid()` and `is_valid_reason()` 2nd argument: okay, let's postpone it for now.  
It is an easy fix in the code anyways (more work on the documentation side).

---

## Comment 30

> Username: mkaravel  
> Created_at: 2015-02-21 14:45:01 UTC  
> Updated_at: 2015-02-21 15:55:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75374528  

Yes, it is a weird name, I agree with you.  
When you suggested it, I looked up the PostGIS manual and found it. This was the reason I decided to name it this way.  
BTW, I like the one-line description of the function in the PostGIS manual. I will most probably use that in our documentation as well.

---

## Comment 31

> Username: mkaravel  
> Created_at: 2015-02-21 14:59:30 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122626  

I have to admit I did not think of is as an advantage, but you are right.  
  
Here is one specific advantage I can think of: right now we allow duplicate points, and this is done via a template parameter that is by default set to `true` (allow duplicate points). If we switch the default value to `false`, then different visitors can allow or disallow duplicate points, and this can be done at run-time instead of compile time.  
  
So here is what I propose: change the interface of the visitor's `apply` methods to return the boolean success/failure value, which will then be used by `is_valid()` as the return value.  
Also change the default value of the `AllowDuplicates` template parameter of `dispatch::is_valid` to `false`, and modify the two visitors we have now to accept duplicate points as valid. The drawback I see here is that `is_valid()` will now check for duplicate points, which means a small time overhead (personally, I am okay with this additional overhead).  
  
What do you think?

---

## Comment 32

> Username: mkaravel  
> Created_at: 2015-02-21 15:05:16 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122660  

I went through the values of the `validity_failure_type` enum. I prefer them as they are now: "holes" are the inner rings, and "interior" refers to the interior of the geometries.  
  
I can replace `holes` by `interior_rings` is that is preferable for you.

---

## Comment 33

> Username: mkaravel  
> Created_at: 2015-02-21 15:09:12 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122693  

Second thoughts: we never use "holes" in BG, but rather "interior rings".  
So I will just simply go ahead and rename "holes" to "interior rings": this way it will be consistent with the rest of the library.

---

## Comment 34

> Username: mkaravel  
> Created_at: 2015-02-21 15:14:09 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122714  

I just grepped the BG source code.  
The word "holes" is used both in comments and code, but only internally.  
Given that the validity failure enum values are visible to the user and error messages are part of the output, I prefer to change them as I mention in my previous message,

---

## Comment 35

> Username: barendgehrels  
> Created_at: 2015-02-21 15:49:46 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25122985  

Agreed, thanks.

---

## Comment 36

> Username: barendgehrels  
> Created_at: 2015-02-21 15:51:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75377451  

OK, that description explains also the name a bit, indeed

---

## Comment 37

> Username: mkaravel  
> Created_at: 2015-02-21 18:32:55 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25124336  

Done. see commits: 5a47531, 004b5af, ef32b10 and 31ecf74.

---

## Comment 38

> Username: mkaravel  
> Created_at: 2015-02-21 18:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75385374  

As far as I can tell, there is one more remaining issue: whether the policy's `apply` methods should return a boolean.  
@barendgehrels Could you please comment on the suggestion I made?

---

## Comment 39

> Username: barendgehrels  
> Created_at: 2015-02-21 18:55:52 UTC  
> Updated_at: 2015-02-24 15:46:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25124551  

I'm OK with this proposal. Yes it makes it more generic - probably you don't even need the AllowDuplicates template parameter anymore? Because it's then the visitors responsibility (it can still be compiletime then, I think)

---

## Comment 40

> Username: mkaravel  
> Created_at: 2015-02-24 14:08:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75761749  

Changes implemented in today's commits:  
- The handling of spikes, for linear geometries, is now also done by the visitor.  
- The free function `is_valid_reason()` has been removed and in its place an overload of `is_valid()` that takes a geometry and a (reference to a) string has been introduced.  
- The documentation for the new overload above has been added.  
- Two more free function overloads have been added for `is_valid()`: one that takes a geometry and a (reference to a) visitor, and one that takes a geometry a (reference to a) validity failure type. Both these functions are still undocumented. They will be documented in the boost 1.58 time frame if time permits.  
- The `is_simple()` has been modified to use an appropriate policy in order to check for simplicity failures related to spikes and duplicate points.

---

## Comment 41

> Username: mkaravel  
> Created_at: 2015-02-24 15:47:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75781285  

In commit 1917641, the `failing_reason_policy` is modified so it prints more information about the invalid self-intersection points found.

---

## Comment 42

> Username: barendgehrels  
> Created_at: 2015-02-24 17:38:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75804555  

Thanks for processing all so quickly. The new interface (overload with string) is a good idea.  
I'm OK with merging

---

## Comment 43

> Username: awulkiew  
> Created_at: 2015-02-25 04:42:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25317732  

Is `to_string()` test function really needed when there is identical `validity_failure_type_message()`? This is probably used to test the message but I think it's not really needed, especially if we consider that the message could be extended to e.g. contain some helper data like point coordinates. The `failure_type` would stay the same, so I think it would be sufficient to just compare the failure types and maybe to check if the failure type is consistent with the message, no need to compare the messages.

---

## Comment 44

> Username: awulkiew  
> Created_at: 2015-02-25 04:56:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75905550  

Yes, this is a great addition! And I'm ok with merging. I saw one place where msvc could generate a "constant conditional expression" warning but we can test and fix any warnings after merging.  
  
I also think that the name `is_valid_reason()` is not a good name, for me it'd be a function returning `true` if a reason was valid. I prefer 2 argument `is_valid()`, taking a reference to string. I'm even ok with not documenting the other overload at all, at least until it's explicitly requested by the users (btw we could ask about it on the list).

---

## Comment 45

> Username: barendgehrels  
> Created_at: 2015-02-25 08:26:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#discussion_r25324229  

Looks much better now, returning apply.

---

## Comment 46

> Username: mkaravel  
> Created_at: 2015-02-25 10:05:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/229#issuecomment-75934830  

Thank you both for the comments and for merging.  
I will prepare another PR with the documentation for `validity_failure_type` and the version of `is_valid()` that takes a reference to this enum as second argument. It should be ready later on today.

---
