# #243 - Any interest in a reference card? [Open]

> Username: LegalizeAdulthood  
> Created at: 2025-06-18 22:08:25 UTC  
> Updated at: 2025-07-14 02:00:50 UTC  
> Url: https://github.com/boostorg/parser/issues/243  

In 2016 I created a [reference card for Spirit 2](https://www.boost.org/doc/libs/1_87_0/libs/spirit/doc/refcard/spirit-reference.pdf) with LaTeX.  
  
Is there any interest in such a reference card for Parser?

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-06-30 14:50:12 UTC  
> Url: https://github.com/boostorg/parser/issues/243#issuecomment-3019466494  

yes ;-)

---

## Comment 2

> Username: tzlaine  
> Created at: 2025-07-12 21:16:54 UTC  
> Url: https://github.com/boostorg/parser/issues/243#issuecomment-3066051380  

I'm sure many people would like to see that.  I'm not sure I want to sign up for keeping it up to date, though.  What is the approach you have in mind for maintainability?

---

## Comment 3

> Username: LegalizeAdulthood  
> Created at: 2025-07-13 02:31:28 UTC  
> Url: https://github.com/boostorg/parser/issues/243#issuecomment-3066351623  

I hadn't thought about how it could be generated from the source files.  Since you're using quickbook it's probably going to be difficult if not impossible, but I am not a quickbook expert.  I have some experience with it when I created an alternate version of boost.test documentation which the maintainer eventually adopted in some form.  
  
The one I made for Spirit 2x was created by hand.  
  
How often do the following change?  
- new parsers  
- parser synthetic attributes  
- synthetic attribute rules  
- parser API (e.g. `bp::parse`)  
  
From what I'm seeing, the above items are relatively stable, but boost.parser is a new library so it doesn't have much of a change history with respect to boost releases.

---

## Comment 4

> Username: tzlaine  
> Created at: 2025-07-13 20:40:12 UTC  
> Url: https://github.com/boostorg/parser/issues/243#issuecomment-3067289900  

I agree with the statement that such changes are infrequent, but not with the notion that they will never happen.  The first time such a change occurs, the card will be out of date.  Without a way forward that keeps such a card up-to-date that I can do myself, I don't want to mention it in the official docs.  
  
If you think it's high-visibility enough, maybe just attach it to this thread, and I'll leave it open.

---

## Comment 5

> Username: LegalizeAdulthood  
> Created at: 2025-07-14 02:00:50 UTC  
> Url: https://github.com/boostorg/parser/issues/243#issuecomment-3067533271  

I think the way you solve any such discrepancy is version the reference card, so it refers to a specific version of boost.parser.
