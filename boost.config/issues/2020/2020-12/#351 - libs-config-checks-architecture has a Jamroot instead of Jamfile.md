# #351 - libs/config/checks/architecture has a Jamroot instead of Jamfile [Closed]

> Username: pdimov  
> Created at: 2020-12-28 04:48:56 UTC  
> Updated at: 2020-12-30 18:58:22 UTC  
> Closed at: 2020-12-30 18:58:22 UTC  
> Url: https://github.com/boostorg/config/issues/351  

`libs/config/checks/architecture` has a `Jamroot` instead of `Jamfile`, which causes the `.obj` files from the architecture and address model checks to end up in `libs/config/checks/architecture/bin` instead of `bin.v2` as one might expect. The usual expectation is that all temporary build artifacts end up in `bin.v2`, so deleting it achieves a clean slate. But that's not the case here; deleting `bin.v2` does not remove the architecture and address model build artifacts, which causes subtle problems when e.g. the default address model for a toolset changes (I just spend 2 hours or so trying to figure out why my default address model for the msvc toolset is detected as 32 bit when it definitely is 64 bit).  
  
When I rename `Jamroot.jam` to `Jamfile.jam`, everything seems to work OK, with the temporary .obj files ending up in `bin.v2`. But I'm not sure there wasn't some reason to use a `Jamroot` here (even though the comment in the file says `Jamfile`).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-28 08:47:42 UTC  
> Url: https://github.com/boostorg/config/issues/351#issuecomment-751635740  

I honestly have no idea why that was Jamroot rather than Jamfile: looks like Jürgen Hunold lifted this from elsewhere?

---

## Comment 2

> Username: pdimov  
> Created at: 2020-12-28 13:58:25 UTC  
> Url: https://github.com/boostorg/config/issues/351#issuecomment-751721145  

The copyright in the files says "Steven Watanabe" (@swatanabe). Could have been a standalone example at one point.  
  
I'm 99.4% sure that `Jamfile` is correct here, the only question is whether it will break something. So far everything seems to work locally.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-12-29 17:43:40 UTC  
> Url: https://github.com/boostorg/config/issues/351#issuecomment-752179108  

OK, here's the formal pull request: https://github.com/boostorg/config/pull/352.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-12-29 17:48:29 UTC  
> Url: https://github.com/boostorg/config/issues/351#issuecomment-752180849  

Seems like I spoke too soon. This breaks "b2 headers" for some reason. :-)

---

## Comment 5

> Username: pdimov  
> Created at: 2020-12-29 18:07:19 UTC  
> Url: https://github.com/boostorg/config/issues/351#issuecomment-752187568  

Fixed now.
