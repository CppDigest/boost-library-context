# #22 - Consider eliminating all partial functions [Closed]

> Username: ldionne  
> Created at: 2015-03-22 16:28:51 UTC  
> Updated at: 2015-05-08 20:08:09 UTC  
> Closed at: 2015-05-08 20:08:09 UTC  
> Url: https://github.com/boostorg/hana/issues/22  

Would it be desirable to eliminate partial functions, i.e. functions that fail for some inputs in their domain? For example, `head` can fail when given an empty `Sequence`. Instead of having `head` return an element or fail if the sequence is empty, it could be specified as returning a `Maybe` containing the element or `nothing`. This makes the function "safe", but it's also slightly more cumbersome to use.  
  
Another possibility is to provide two versions of `head`; one which is safe and returns a `Maybe`, and one which isn't and may fail. It could be called something like `head` and `head.safe`.  
  
I'm putting this issue here mostly to get it out of my head (and huge list of local issues I maintain locally), but I've grown less and less in favour of it. If someone has an opinion about this, please comment.

---

## Comment 1

> Username: jfalcou  
> Created at: 2015-03-22 16:33:24 UTC  
> Url: https://github.com/boostorg/hana/issues/22#issuecomment-84649444  

the direct call to head should be the fastest and simplest one. So i'm all for having some head.safe or maybe.head function when i know I want the extra stuff

---

## Comment 2

> Username: ldionne  
> Created at: 2015-03-22 16:36:01 UTC  
> Url: https://github.com/boostorg/hana/issues/22#issuecomment-84650148  

I agree that forcing users to use the Maybe interface for something as simple as `head` makes no sense, so it should be opt-in if at all. This might be a nice feature, but it's super low-priority unless someone can convince me otherwise.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-05-08 20:08:09 UTC  
> Url: https://github.com/boostorg/hana/issues/22#issuecomment-100344389  

Revisiting this, I don't see any real value in eliminating all partial functions, and providing a `.safe` variant systematically seems like a huge pain in the ass for a small gain. Instead, users should just check for the preconditions before calling a function if they don't know whether the preconditions are respected. Closing this.
