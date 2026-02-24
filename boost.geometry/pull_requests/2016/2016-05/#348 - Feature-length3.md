# #348 Feature/length3 [Merged]

> Username: vissarion  
> Created at: 2016-05-19 16:13:49 UTC  
> Updated at: 2016-06-21 14:57:30 UTC  
> Merged at: 2016-05-28 12:08:18 UTC  
> Closed at: 2016-05-28 12:08:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/348  

Length tests for spherical and geographic coordinate systems.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2016-05-19 16:27:03 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63910770  

One new line should be enough.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2016-05-19 16:28:06 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63910950  

Brace should go in next line:  
  
```  
struct geo_strategies  
{  
```

---

## Comment 3

> Username: mkaravel  
> Created_at: 2016-05-19 16:28:53 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63911077  

No space after `std::string`

---

## Comment 4

> Username: mkaravel  
> Created_at: 2016-05-19 16:30:33 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63911331  

Why are these concept asserts here? They should be in the distance code...

---

## Comment 5

> Username: mkaravel  
> Created_at: 2016-05-19 16:31:32 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63911470  

What about empty multi-linestring?

---

## Comment 6

> Username: mkaravel  
> Created_at: 2016-05-19 16:33:03 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63911708  

Should be `std::size_t`. Also spaces are missing (code style).

---

## Comment 7

> Username: mkaravel  
> Created_at: 2016-05-19 16:33:55 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63911820  

Are you normalizing coordinates?

---

## Comment 8

> Username: mkaravel  
> Created_at: 2016-05-19 16:34:16 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63911865  

Same issue: are coordinates normalized (I am referring to longitude here).

---

## Comment 9

> Username: mkaravel  
> Created_at: 2016-05-19 16:34:43 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63911941  

Should mention unit in comment (Km).

---

## Comment 10

> Username: mkaravel  
> Created_at: 2016-05-19 16:35:41 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63912098  

No space after `double`. Also I think the policy is to have no long lines (over 80 characters) and use tabs of 4 spaces (but no tabs should be in code...)

---

## Comment 11

> Username: mkaravel  
> Created_at: 2016-05-19 16:36:06 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63912165  

Add space after `,`

---

## Comment 12

> Username: mkaravel  
> Created_at: 2016-05-19 16:36:43 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63912250  

Why add this line as a comment?

---

## Comment 13

> Username: mkaravel  
> Created_at: 2016-05-19 16:37:42 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63912380  

Replace `size_t` with `std::size_t`.  
Please add spaces before/after `=` and `<`.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2016-05-19 16:38:02 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63912427  

I also think that we add braces even for one-line for loops.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2016-05-19 16:39:54 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63912713  

I do not really see the point of this method.  
You can simply define the array and then use Boost.Range functionality to get what you want...

---

## Comment 16

> Username: mkaravel  
> Created_at: 2016-05-19 16:41:21 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63912938  

Same here: You can simply define the array and then use Boost.Range functionality.  
Even better, it might make sense to combine the test cases of all length unit tests into a single file header and include that in all tests.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2016-05-19 16:42:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#issuecomment-220382796  

Hi Vissarion. Made a few comments, primarily on the coding style...

---

## Comment 18

> Username: awulkiew  
> Created_at: 2016-05-19 17:05:34 UTC  
> Updated_at: 2016-05-23 10:19:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r63916755  

Like this:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/overlay/overlay_cases.hpp

---

## Comment 19

> Username: vissarion  
> Created_at: 2016-05-20 11:05:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#issuecomment-220578240  

Hi Menelaos. Thanks for the comments.

---

## Comment 20

> Username: awulkiew  
> Created_at: 2016-05-23 10:49:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#discussion_r64202564  

Empty MLs test case is now added below this line but GitHub shows this comment anyway.

---

## Comment 21

> Username: awulkiew  
> Created_at: 2016-05-23 10:49:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#issuecomment-220947921  

Thanks, I'm ok with merging.

---

## Comment 22

> Username: mkaravel  
> Created_at: 2016-05-23 16:43:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/348#issuecomment-221027362  

Same here.

---
