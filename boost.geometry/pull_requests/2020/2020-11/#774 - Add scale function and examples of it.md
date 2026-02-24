# #774 [svg] Add scale function and examples of it [Merged]

> Username: barendgehrels  
> Created at: 2020-11-12 13:36:17 UTC  
> Updated at: 2021-06-02 09:47:53 UTC  
> Merged at: 2021-02-19 09:43:17 UTC  
> Closed at: 2021-02-19 09:43:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/774  

Adds the `scale` and I think that's really awesome, because in most cases I add geometries with a buffer, but this is way more convenient.  
At the same time it allows users to write svg definitions, for example arrows (but it can be anything).

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2020-11-12 13:37:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#issuecomment-726082375  

I've currently not the configuration to build documentation, is it possible to check this online - or can some of you check this?  
Thanks.

---

## Comment 2

> Username: vissarion  
> Created_at: 2020-11-13 16:00:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#issuecomment-726845783  

> I've currently not the configuration to build documentation, is it possible to check this online - or can some of you check this?  
> Thanks.  
  
This should do the online check and looks ok  
 https://github.com/boostorg/geometry/pull/774/checks?check_run_id=1390649812

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2020-11-13 16:01:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/774#pullrequestreview-530186285  

Nice addition!  
EDIT:  
Since this is an enhancement the PR should be labelled 1.76 right?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2020-11-18 09:14:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#issuecomment-729544159  

> Nice addition!  
> EDIT:  
> Since this is an enhancement the PR should be labelled 1.76 right?  
  
Yes, it's too late for 1.75

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2020-12-22 18:28:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#issuecomment-749707007  

@awulkiew Are you OK with this new functionality?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2021-02-19 02:08:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#issuecomment-781763980  

@barendgehrels Yes, I'm ok with it. :)

---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-02-19 02:09:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/774#pullrequestreview-593803206  

📁 include/boost/geometry/io/svg/svg_mapper.hpp

```diff
 378 |+       A scale of 1.0 doesn't change any scaling, but still writes the SVG header.
 379 |+     */
 380 |+     void scale(double scale = 1.0)
```

> Username: awulkiew  
> Created_at: 2021-02-19 02:09:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#discussion_r578875758  

What's the purpose of the default parameter?

> Username: barendgehrels  
> Created_at: 2021-02-19 09:43:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#discussion_r579054001  

Calling `scale()` writes the SVG header (without a specific scaling). Sometimes you want to write the header - because you want to add definitions afterwards, before you do the first call to `map`.  
  
It might be seen as a trick and I can add another method which does that more explicitly (`write_header`? Suggestions?). However, the problem then is that it is instead of just scale (you can't do both), which is also not so convenient.  
  
I'll merge it now but I'm open to suggestions for enhancement. It's a function for end users, so it should be as good as possible.

> Username: awulkiew  
> Created_at: 2021-02-19 14:49:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#discussion_r579239658  

Yes, the purpose of scaling function is to scale, not to write headers.  
  
I gave it more thought and I see a problem with `scale()` as it is. Typically such function would be implemented if it was needed to scale several times, e.g. add geometries, scale them, add more geometries, scale all of them again, transform, etc. But here this sets the BB of all of the geometries which means this rather is `set_scale()`, i.e. it sets the scale for the whole image. It also has to be called at the right moment because otherwise it'd break consistency of the data, e.g. when `add()` -> `scale()` -> `add()` was called. And since it also writes headers this double downs on the concept that this has to be called at the correct moment.  
  
I understand that this is more or less a legacy problem caused by the overall design of the `svg_mapper` which I think should be redesigned altogether, but that's a different story.  
  
But if scaling the whole image for convenience is the main issue here, why not simply allow passing scale in the constructor, keeping it and then simply using when the geometries are actually drawn/written? With the default being `0.95` for convenience from the start.

> Username: barendgehrels  
> Created_at: 2021-02-24 11:44:49 UTC  
> Updated_at: 2021-02-24 11:44:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#discussion_r581890887  

> But if scaling the whole image for convenience is the main issue here, why not simply allow passing scale in the constructor, keeping it and then simply using when the geometries are actually drawn/written? With the default being `0.95` for convenience from the start.  
  
Having the scale in the constructor is possible and safer.  
And the moment that the header is written can actually easy be changed. I'll make a follow up.  
  
>  I understand that this is more or less a legacy problem caused by the overall design of the svg_mapper which I think should be redesigned altogether  
  
I don't know if it should. But it might.  
It's an often used part of our library. It's simple but people love it (I know people who love it). It's true that we can add more functionality - SVG domain is huge, and it can even be a library on its own. But now it is convenient for debugging purposes, like we have in many unit tests. And still powerful.  
Because it's used a lot we can't change the interface (apart from adding something, like scale) of this `svg_mapper`. So if you want to redesign it (and I'm not against that), it should probably be another svg mapper

> Username: awulkiew  
> Created_at: 2021-02-24 12:57:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/774#discussion_r581936632  

> I don't know if it should. But it might.  
  
Ok, maybe I was not clear enough. I would not want to remove `svg_mapper`. If anything I'd rather propose to develop an additional utility replacing it or modify the existing one if possible.  
  
The main objection I have is that this utility tries to be high level and low level at the same time. It was probably implemented at some point to solve a very specific problem in the development of the library, quickly.  
  
In all of the use cases I saw in the library we use it as more or less high level utility for the purpose of drawing some number of geometries for debugging. Something like that could be done with one function call. There is no need to divide `add` and `map` into multiple steps, it should be clear when the file is written exactly, etc.  
  
On the other hand if we wanted to have full control over the creation of the SVG file then the interface should reflect that. We should be allowed to create an image of a specific size explicitly, to write the header manually to write geometries one by one, or multiple times. There is no need for a struct holding any information in such case. I know we have `svg` manipulator but this can be used only to output single geometries and cannot be used to create a file conveniently. Unless I'm missing something.  
  
Anyway, I wanted only to make my point clear. I do not think that there is the need to change this ATM.


---
