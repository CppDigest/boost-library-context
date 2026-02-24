# #520 Add image constructor from compatible view [Merged]

> Username: sdebionne  
> Created at: 2020-10-02 09:02:48 UTC  
> Updated at: 2020-11-10 08:10:44 UTC  
> Merged at: 2020-10-11 19:38:37 UTC  
> Closed at: 2020-10-11 19:38:37 UTC  
> Url: https://github.com/boostorg/gil/pull/520  

### Description  
  
For convenience, this PR introduces a new image constructor to "materialize" (in the SQL sense) an image_view.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: lpranam  
> Created_at: 2020-10-06 10:04:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/520#pullrequestreview-502779824  

Looks good to me

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-10-06 12:01:36 UTC  
> Url: https://github.com/boostorg/gil/pull/520#issuecomment-704221202  

Although this looks good to me too, I'm having issue about the terminology vs the code here.  
  
The subject of this PR says "from compatible view" brief look at the code immediately raised me a question:  
- Why not to `enable_if` with `views_are_compatible` (de-facto `pixels_are_compatible`)?  
  
In GIL, definition of view/pixel compatibility is based on color space compatibilty and the latter says:  
```  
struct color_spaces_are_compatible : std::is_same<CS1, CS2> {};  
```  
  
which obviously is not equivalent of what this PR proposes.  
  
In GIL, we may want to distinguish view/pixel compatibility from view/pixel convertibility.  
  
Would you agree that the "from compatible view"  should read "from convertible view"?

---

## Comment 3

> Username: sdebionne  
> Created_at: 2020-10-06 15:11:35 UTC  
> Url: https://github.com/boostorg/gil/pull/520#issuecomment-704338733  

> Why not to `enable_if` with `views_are_compatible` (de-facto pixels_are_compatible)?  
  
Right, I forgot about `views_are_compatible` and that would be better to use GIL traits to check compatibility between the target image and the source view.  
  
How about `typename std::enable_if<views_are_compatible<image_view<Loc>, view_t >::value, int>::type = 0>`?  
  
Is `std::convertible<P1, P2>`  (*P1 can be converted to P2 using implicit conversions*) different from `pixels_are_compatible< P1, P2>` (Compatible pixels can be *assigned and copy constructed from one another*)?  
  
Note that `allocate_and_copy` uses `uninitialized_copy_pixels` internally, not `copy_and_convert_pixels`. So is it compatibility or convertibility?  
  
I am confused. Maybe we need some examples to clarify "convertible" vs "compatible"? My guess is  
  
gray8 -> gray16 : convertible?  
rgb8 -> bgr8  : compatible?

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-10-09 09:33:55 UTC  
> Url: https://github.com/boostorg/gil/pull/520#issuecomment-706077598  

@sdebionne Sorry for delay.  
  
> How about `typename std::enable_if<views_are_compatible<image_view<Loc>, view_t >::value, int>::type = 0>`?  
  
Well, you know it best if this would be suitable for your intention in this PR.  
  
> Is `std::convertible<P1, P2>` different from `pixels_are_compatible<P1, P2>`?  
  
Yes, to me it is different.  
  
The `pixels_are_compatible` is stricter, in terms of `std:is_same` for pixel components.  
For non-compatible, but convertible, an explicit conversion via e.g. `copy_and_convert_pixels` is necessary,  
  
> I am confused.  
  
Me too :-)  
  
> Maybe we need some examples to clarify "convertible" vs "compatible"?  
  
Yes we do!  
  
I guess, we could also start developing some more systematic system of type traits in GIL:  
  
```  
gil::is_color_convertible<T1, T2>  
gil::is_color_compatible<T1, T2>  
```  
  
where `T1` and `T2` stand either for image, view, pixel, color space and even down to channel type.  
Leaving the existing `views_are_compatible` and `pixels_are_compatible` alone, for now.  
  
What do you think?  
  
> My guess is  
>  
> gray8 -> gray16 : convertible?  
  
That is how I understand it.  
  
> rgb8 -> bgr8 : compatible?  
  
That is how I understand it, thanks to semantic kth-dereferencing of channels.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2020-10-09 15:00:22 UTC  
> Url: https://github.com/boostorg/gil/pull/520#issuecomment-706232718  

Thanks for the clarification! I'll go for compatibility with this PR and use the more strict `pixels_are_compatible`.

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-10-09 15:36:12 UTC  
> Url: https://github.com/boostorg/gil/pull/520#issuecomment-706252041  

Cool. What do you think about the traits idea? Is this anything you'd see useful, a good direction, in future?

---

## Comment 7

> Username: mloskot  
> Created_at: 2020-10-11 19:38:44 UTC  
> Url: https://github.com/boostorg/gil/pull/520#issuecomment-706757318  

Thanks @sdebionne

---
