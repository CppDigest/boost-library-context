# #418 - Review: public types [Closed]

> Username: alandefreitas  
> Created at: 2022-08-16 23:57:49 UTC  
> Updated at: 2022-08-22 16:56:57 UTC  
> Closed at: 2022-08-22 16:56:57 UTC  
> Url: https://github.com/boostorg/url/issues/418  

From the review (for reference)  
  
> I'm not very keen on exposing the parsing infrastructure and helper  
functions (other than percent encoding/decoding) as public API,  
as I think it violates the principle of API surface minimization.  
I understand that these parsing facilities may help libraries  
such as HTTPProto, but I'm not sure if the general public should  
be using them. I may be missing something, but I haven't  
found the example on magnet links very compelling - I feel  
the same result could be achieved in a cleaner way by using  
the higher level API. If the parsing facilities are to be kept  
public, I think we need a more compelling example for them.  
From reading this comment  
https://github.com/CPPAlliance/url/issues/379#issuecomment-1212026752  
I get the impression that some schemes have different reserved  
character sets, and that these functions help with that problem  
> - maybe an example of such an scheme could be helpful.  
  
> I can see other public types in the reference such as recycled,  
recycled_ptr and aligned_storage, which again have the feeling  
should not be exposed as public types.  
  
Comments  
  
> Some enterprising folks are going to want to use them to do things  
like say, parse cookies, parse URLs-within-URLs (like when you put a  
whole URL in the fragment), parse a larger grammar that contains URLs,  
or even just parse things that are not URLs but they are already using  
Boost.URL (and Boost.Beast and Boost.JSON) for their server and the  
algorithms do exactly what they want with the performance and runtime  
characteristics which are ideal for the use-case  
  
> That (a BSL-licensed repository) is the equivalent of just making them private. Which I could  
easily do, just remove them from the docs and that's that. Submodules  
in boost library include/ directories don't work out too well (I'm not  
sure they are even allowed).
