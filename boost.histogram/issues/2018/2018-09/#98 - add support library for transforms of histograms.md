# #98 - add support library for transforms of histograms [Closed]

> Username: HDembinski  
> Created at: 2018-09-21 09:40:21 UTC  
> Updated at: 2018-11-28 20:41:16 UTC  
> Closed at: 2018-11-28 20:41:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/98  

* reduce_to -> project  
* rebin  
* sum  
* compute density  
* shrink

---

## Comment 1

> Username: jpivarski  
> Created at: 2018-09-21 12:23:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-423513212  

These are good, but another fundamental one is "select" for eliminating bins without combining them. For instance, starting with 10 regular bins from –5 to 5, `select` from index 3 to index 9 would give 6 regular bins from –2 to 4.  
  
There are two ways to handle overflow/underflow: `select` could either chop them off like other bins (which could give rise to a case that has an underflow but no overflow) or `select` could merge eliminated bins into the corresponding overflow/underflow, should it exist. In the former case, overflow/underflow represent normal bins that can be selected like anything else. In the later case, overflow/underflow represents bins that are "gone," either because they weren't in the original binning it because they were selected away.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-09-24 09:36:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-423921050  

Excellent. I think "select" should add the eliminated bins to underflow/overflow like you say, if those bins are present, because anything else would violate the rule that a sum over a histogram with under/overflow bins always reproduces the number of fills.

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-09-24 09:38:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-423921536  

I wonder, "select" and "project" are independent, right? Then it would make sense to have both as separate functions, instead of one function that "projects" and "selects".

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-09-24 09:40:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-423921952  

Perhaps a better name for "select" is "shrink".

---

## Comment 5

> Username: jpivarski  
> Created at: 2018-09-24 12:40:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-423960719  

At first, I would have said that projecting (summing over all bins in an axis to reduce the dimensionality), selection (simply dropping bins without summing), and rebinning (merging bins by summing and dropping) are all independent, but when I thought about it closely, they all seem to be special cases of a single function: rebin.  
  
Rebin takes an input axis of N bins and produces an output axis of M bins (M < N) by performing M sums over consecutive bins. The rebinning may be regular ("reduce the number of bins in this regular axis by a factor of 2") or irregular ("merge the first three bins into one, the next two bins into another, the next four bins into a third, and the last eight bins into the last"). Only regular rebinning on a regular axis can result in a regular axis; the other cases all result in an irregular axis.  
  
Project could be thought of as an extreme rebin, in which the output size M = 1. An entire axis becomes one bin (including the underflow and overflow). A one-bin axis is not particularly useful, so we reshape it away (in the Numpy sense: `shape (5, 1, 12) → (5, 12)`). That reshaping is just changing the metadata, not part of the operation on bin contents themselves.  
  
If 'select' truly drops bins, never to be seen again, then it's not a special case of rebin. But we both like the idea of merging the dropped bins into underflow or overflow (depending on whether the select cuts off the left or the right), and this makes it an irregular rebin. For instance, dropping the first 5 of 10 bins is an irregular rebinning that merges bins 0 until 5 and the original underflow, if present, into a single underflow bin. (Note: this is a way to end up with an underflow bin and no overflow bin. Would an empty overflow be added in this case?)  
  
Computing density is completely separate, but it belongs to a class of operations like adding histograms or multiplying by a scalar.

---

## Comment 6

> Username: jpivarski  
> Created at: 2018-09-24 12:44:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-423961882  

I think "shrink" would be less clear, though I'm open to alternatives to "select." The data analyst using this package is going to think about the operation as a post-aggregation filtering of the data, and that's why I picked a filter-like word. "Filter" itself is often used as the operation on pre-aggregated data, and a general filter on post-aggregated data is impossible, so I'd avoid that word. "Cut" is HEP jargon, and it means the same thing as filter. "Select" seemed to be the only one left.

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-09-24 14:49:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424001927  

I think "shrink" is better if the removed bins are merged into under/overflow, exactly because nothing is filtered away / dropped. I would avoid filter language, at least if we maintain that no fill is lost.  
  
The STL vector already has "shrink_to_fit"  
https://en.cppreference.com/w/cpp/container/vector/shrink_to_fit  
  
so the word "shrink" is already established in this context.

---

## Comment 8

> Username: HDembinski  
> Created at: 2018-09-24 14:50:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424002186  

Also in case of STL vector, nothing is lost by shrinking.

---

## Comment 9

> Username: jpivarski  
> Created at: 2018-09-24 16:35:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424040495  

Maybe "collapse" instead of "shrink"? If all three operations (project, select, rebin) are all really forms of rebinning, perhaps there could be just one function and then we'd only need to name one function.  
  
Maybe the usage of "shrink" is an STL convention I'm not familiar with, and therefore people in the C++ culture would see "shrink" as the kind of thing we're talking about. It could just be my unfamiliarity, and thus it's up to C++ experts to name it.  
  
Or, for affinity with data analysts, the single function could be named "rebin" with documentation explaining how to rebin irregularly (for the "select" case) and how to get a projection from rebinning.

---

## Comment 10

> Username: HDembinski  
> Created at: 2018-09-24 16:39:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424041665  

If it is not technically necessary (for performance) to combine these functions into one, then they should be separate as a general rule. Divide-and-conquer.

---

## Comment 11

> Username: HDembinski  
> Created at: 2018-09-24 16:40:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424042050  

I still prefer "shrink" over these other options. It is pretty self-explanatory.

---

## Comment 12

> Username: jpivarski  
> Created at: 2018-09-24 16:49:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424045164  

It's not a performance thing, it's a DRY thing. These three operations may be conceived of differently by the data analyst, but they're special cases of the same "irregular rebin" operation.  
  
I just read the [reference for STL shrink](http://www.cplusplus.com/reference/vector/vector/shrink_to_fit/) and it seems to be a different operation. STL shrink optimizes an internal data structure; it doesn't change the number of elements visible to the user (accessible to `at` or `size`, for instance).  
  
What we're talking about here changes the number of elements visible to the user: the number of bins and how they access them. STL's "shrink" doesn't seem to be a good analogy. Nor would "reserve" for the same reason.  
  
However, "resize" would be a good analogy. How about that?

---

## Comment 13

> Username: HDembinski  
> Created at: 2018-09-24 22:25:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424145758  

"resize" implies that you can make the axis larger. But you can only make it narrower.

---

## Comment 14

> Username: HDembinski  
> Created at: 2018-09-24 22:29:19 UTC  
> Updated at: 2018-09-24 22:29:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424146644  

DRY would apply if shrinking and projecting shares a lot of code, which I do not see. I will see it when I implement these. In the C++ world, short functions with a single purpose are preferred over large do-it-all functions steered by a myriad of keywords - as it is frequent in Python.

---

## Comment 15

> Username: jpivarski  
> Created at: 2018-09-24 22:34:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424147828  

Yeah, I didn't mean the megafunction. I mean that projection is a special case of rebinning and selection is a special case of rebinning, so once general rebinning is implemented, the other two can be one-line calls into the general function. (Projection may need another step to reshape the data so that you don't end up with a one-bin axis.)

---

## Comment 16

> Username: HDembinski  
> Created at: 2018-09-25 07:16:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424233150  

I am worried that the interface of this rebin function will be complex if it can do everything. How would the arguments of this function look like in C++? I think from the user side it is easier if there are separate functions project and shrink. Smaller functions are easier to debug and document and have simpler inputs.

---

## Comment 17

> Username: jpivarski  
> Created at: 2018-09-25 11:09:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-424301691  

I agree about different public-facing APIs. I'm just saying that two of those functions can be one line calls into the other.  
  
In fact, they should be functions with different public-facing names because data analysts would think about the three functionalities in different ways.

---

## Comment 18

> Username: HDembinski  
> Created at: 2018-11-15 07:53:32 UTC  
> Updated at: 2018-11-15 08:00:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-438949326  

Hi Jim, I am now working this issue and I fully came around to your view, that combining all functionality into one function call is necessary, because otherwise you need to create superfluous temporary histograms.  
  
I am now wondering about the interface and the name of such a function. I think `reduce` is a good generic name for the super function. It should allow you to project, shrink axes, and/or rebin them. All of these operations boil down to copying and summing cell contents from the original histogram into the new histogram. This is best done in one go.  
  
What do you think about this interface, explained by examples:  
```  
// keep first and third axis of 3d histogram, sum over second  
auto reduce(const histogram& h3d, sum_over(1));  
// keep all axes, but rebin the first axis, merging two old bins into one new bin  
auto reduce(const histogram& h3d, rebin(0, 2));  
// keep all axes, shrink the second axis so that covers the interval [lower, upper) [corresponding indices are found automatically]  
auto reduce(const histogram& h3d, shrink(1, lower, upper));  
// all these can be combined  
auto reduce(const histogram& h3d, sum_over(0), shrink(1, lower, upper), rebin(2, 3));  
```

---

## Comment 19

> Username: HDembinski  
> Created at: 2018-11-15 08:01:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-438950992  

Implementing all this this is going to be difficult. Especially if we also want to allow multiple operations on the same axis. e.g.  
```  
// shrink first axis and then rebin  
auto reduce(const histogram& h3d, shrink(0, 0.1, 0.3), rebin(0, 2));  
```

---

## Comment 20

> Username: jpivarski  
> Created at: 2018-11-15 13:27:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-439039548  

You're closer to the implementation right now so you know better than me, but I don't see why there's a performance overhead from just having  
  
```  
auto rebin(const histogram& h3d, 0, 2)  
```  
  
etc. (Rebin, shrink, and sum_over are free functions that all take a histogram as their first argument and return a histogram.)  
  
My argument had been that all of these operations are different parametrizations of a single general operation, which may be a hidden helper function for all three or exposed publicly for power-users.  
  
What you're doing looks like fusing operations to save intermediate histograms. It's not obvious to me that this is necessary, but since you're looking at it right now and it looks to you like it is, I can believe it.

---

## Comment 21

> Username: HDembinski  
> Created at: 2018-11-15 19:46:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-439166912  

Ok, but just to explain better what I mean. Let's say you want to rebin the first axes and shrink the second. Then you do:  
```  
auto h2 =  rebin(h1, 0, 2);  
auto h3 = shrink(h2, 1, 1.5, 2.5);  
```  
This creates one temporary histogram, which could be avoided in principle, if there was a call that can do both at once, rebinning and shrinking, which as you say, are different aspects of the same more generic algorithm. If you only ever want to do either - shrinking, projecting, or rebinning, never several of these at once - then having several small functions is ok.

---

## Comment 22

> Username: jpivarski  
> Created at: 2018-11-15 23:50:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-439232221  

Oh, I see— fusing operations could help. It's hard to imagine a user thinking in terms of rebinning one dimension and projecting away another at the same time, but a higher-level library might accumulate operations and run them all at once for performance. Interface-wise, for direct users of this library, there should be an interface that allows a rebin call without having to nest it within a reduce framework.

---

## Comment 23

> Username: HDembinski  
> Created at: 2018-11-19 09:16:55 UTC  
> Updated at: 2018-11-19 09:18:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-439822810  

In C++, they have this "zero-overhead" philosophy. A good interface should not do something inefficient by design, it should always solve the problem in the most efficient way. I cannot know how my users are using the library, but if they want to do shrinking and rebinning along several axes, they should be able to do that in one go. I can guarantee that the Boost guys would complain if I designed an interface which forces the user to make temporary copies of histograms if those can be avoided. Since the histogram memory could be very large for a high-dimensional histogram, it is actually worthwhile to avoid making full copies.  
  
On the develop branch, I now have a `project` function, which does projections only, and a `reduce` function, which does rebinning and shrinking. The reason why `project` is separate is technical, `project` can change the type of the returned histogram while `reduce` cannot. You see, I also support histograms which have a static axis configuration, where changing the axes changes the class type of the histogram. Projecting changes the type of such histograms, while rebinning and shrinking never does. That's why rebinning and shrinking can have a simpler function signature.   
  
Syntax looks like this:  
```  
auto h = ... // some 2d histogram  
// rebin first axes, shrink second; first argument is always axis index  
auto h2 = reduce(h, rebin(0, 2), shrink(1, 3.5, 5.2));  
// you can also shrink and rebin in one step, it shrinks first and then rebins  
auto h3 = reduce(h, shrink_and_rebin(0, 3.5, 5.2, 2));  
```  
The arguments to `shrink`(other than the axis index) is the range of values that should be kept. The arguments for `rebin` is the number of bins that should be merged.

---

## Comment 24

> Username: HDembinski  
> Created at: 2018-11-19 09:21:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-439824387  

So I have all the algorithms from the to-do list of the first post except computing the density. That will be done next.

---

## Comment 25

> Username: HDembinski  
> Created at: 2018-11-28 20:41:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/98#issuecomment-442597013  

All implemented in develop. `density` is a method of the new `indexed` range
