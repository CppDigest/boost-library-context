# #437 fixed CSS property in doc/math.css [Merged]

> Username: suyash-patil  
> Created at: 2020-09-18 11:21:09 UTC  
> Updated at: 2020-09-29 12:00:33 UTC  
> Merged at: 2020-09-26 21:38:15 UTC  
> Closed at: 2020-09-26 21:38:15 UTC  
> Url: https://github.com/boostorg/math/pull/437  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-09-18 12:33:19 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-694840507  

@suyash-patil : Is there a place in the docs that inspired this fix? I'd like to validate it . . .

---

## Comment 2

> Username: suyash-patil  
> Created_at: 2020-09-18 12:35:52 UTC  
> Updated_at: 2020-09-18 12:37:38 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-694841651  

As I was going through the files, I saw that this is an invalid syntax in CSS. That is why I fixed this.

---

## Comment 3

> Username: pabristow  
> Created_at: 2020-09-18 13:33:27 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-694870464  

As far as I know, the existing code 'works' - in that things that are supposed to be red are displayed as red.  
It would clearly be better not to have invalid items, even if they work at present.  
  
@suyash-patil  Can you explain what tool or knowledge found this?  
  
But I'm re-building some Quickbook docs soon and I'll check out the proposed correction to make quite sure it still works.

---

## Comment 4

> Username: suyash-patil  
> Created_at: 2020-09-18 13:55:37 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-694882900  

@pabristow: Actually, I opened the file and the text editor highlighted the syntax error. It has a value (inline-block) but has no key (should be "display"). In that way, I figured out the error.

---

## Comment 5

> Username: pabristow  
> Created_at: 2020-09-18 14:03:19 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-694887374  

Thanks - sounds simple enough.  I'll check it out unless someone else beats me to it.

---

## Comment 6

> Username: pabristow  
> Created_at: 2020-09-21 09:44:12 UTC  
> Updated_at: 2020-09-21 10:55:41 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-696010171  

I've start to look at this, but may be a little more complicated than I thought.  
  
@suyash-patil is correct that there is a missing display:   
  
The color feature was a late addition to Quickbook, used in the production of the math docs, so we do not seem to have used it much.  The main use is in table of performance where worse performance is show in red and better in green or reference in blue.  
  
There are a few uses in the text  like "Warning code may explode" ;-)  
  
The other use is a tick or cross symbol, but this is done using a Unicode symbol, so counts as text?  
  
I am puzzled at why red has been chose for special treatment?  
  
From my reading of  
  
https://stackoverflow.com/questions/3043021/is-there-any-guide-on-when-to-use-displayblock-when-inline-and-when-inline-b#:~:text=The%20use%20cases%20for%20block,it%27s%20used%20naturally%20for%20images.  
  
suggests that display:inline would be most suitable when it is a text item like "1.2345".  
  
(whereas display: inline-block for a graphic, but does allow height and width specification so perhaps this is right?)  
  
But I am not an expert on this.  Any more informed views?  
  
@jzmaddock probably remembers something about this?  
  
And I presume that the same 'display: inline' should be applied to all colors, not just red?  
  
(Aside: colors are also use in Boost. Multiprecision and probably other libraries too - this should perhaps be in boostbook.css?)

---

## Comment 7

> Username: suyash-patil  
> Created_at: 2020-09-26 16:21:20 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-699516514  

@pabristow Can you please tell me how to pass the travis CI build test at the time of making PRs?

---

## Comment 8

> Username: pabristow  
> Created_at: 2020-09-26 16:43:16 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-699518904  

In the absence of comments on this issue, I propose to set all the colors to use your suggestion, so I don't think you need to bother further with a PR for a trivial (but useful) proposal, for which thanks.  
  
(And anyway, I am a Travis novice and I don't know the answer to your question :-( )

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2020-09-26 17:48:44 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-699526781  

You can ignore the Travis failure - someone has merged code that had failures - I've left a message about that elsewhere.  In the meantime I see no reason not to just merge this as is?

---

## Comment 10

> Username: pabristow  
> Created_at: 2020-09-29 12:00:33 UTC  
> Url: https://github.com/boostorg/math/pull/437#issuecomment-700655709  

commit commit f304c8379448afac2f1dcb4ba68b969fc09af145 adds display:inline-block; for all colors in math.css.  
  
(Some experiments show that color can be applied to many items, including equations in red using the Quickbook [role red Some red text].  Could be more widely used?)

---
