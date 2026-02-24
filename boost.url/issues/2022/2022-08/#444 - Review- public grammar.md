# #444 - Review: public grammar [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 06:15:35 UTC  
> Updated at: 2022-09-11 23:46:14 UTC  
> Closed at: 2022-09-11 23:46:14 UTC  
> Url: https://github.com/boostorg/url/issues/444  

> I find the creation of a mini-parsing library and the expectation that  
> users will use this mini-library to write their own parsers to be  
> somewhat problematic.  No one is going to use your mini-library for  
> serious parsing work (except perhaps for modifications to the way URL  
> does its parsing).  It does have proper error reporting, for one  
> thing.  There are other equally obvious limitations.  If the parsing  
> were an implementation detail, or if you were only providing the  
> parsing lib for extensions to your existing parsing, what you've  
> provided would be enough.  However, you also claim that you can use  
> URL as part of a larger parser, and I don't think that's realistic.  
  
There are other similar comments.  
  
The idea is valid. We have a few problems with the mini-library.  
  
- We could incubate it as a mini-library anyway and reuse it in http-proto with no problem.  
- The API keeps changing all the time and making it public makes things much harder.  
- Other people don't seem not very interested in it at all.  
- People who want general parsers would probably go straight to spirit or write their own thing anyway.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-22 06:40:14 UTC  
> Url: https://github.com/boostorg/url/issues/444#issuecomment-1221915886  

More comments:  
  
> Regarding the parser interface, I feel this should mature into its own library.  
> I don't see myself using it currently, and would consider it a pure  
> implementation w.r.t. of Boost URL. I have not reviewed this section of the  
> library and only skimmed the docs.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-22 16:56:53 UTC  
> Url: https://github.com/boostorg/url/issues/444#issuecomment-1222637709  

I just noticed https://github.com/CPPAlliance/url/issues/418 as a duplicate. Moving the comments here:  
  
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

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-22 20:56:31 UTC  
> Url: https://github.com/boostorg/url/issues/444#issuecomment-1223020748  

> I skipped / skimmed the Customization and Concept pages.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-01 23:00:42 UTC  
> Url: https://github.com/boostorg/url/issues/444#issuecomment-1234871666  

We already have a user of this part of the library by the way

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-11 23:46:14 UTC  
> Url: https://github.com/boostorg/url/issues/444#issuecomment-1243070519  

Definitely need this
