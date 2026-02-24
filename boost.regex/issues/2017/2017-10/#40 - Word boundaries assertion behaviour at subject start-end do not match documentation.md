# #40 - Word boundaries assertion behaviour at subject start/end do not match documentation [Closed]

> Username: mawww  
> Created at: 2017-10-09 04:54:00 UTC  
> Updated at: 2018-07-21 14:20:47 UTC  
> Closed at: 2018-07-21 14:20:46 UTC  
> Url: https://github.com/boostorg/regex/issues/40  

Hello,  
  
According to the documentation, using `match_not_bow` means `\b` will not match at [first,first], however the code seems to treat it as "*(first)-1 is a word character", leading to a different behaviour.  
  
For example I'd expect `\b` not to match anything given the subject `-` and `match_not_bow`, but the current implementation does find an empty match at the subject begin. The same behaviour can be observed as subject end with `match_not_eow`.  
  
Cheers,  
  
Maxime.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-21 14:20:46 UTC  
> Url: https://github.com/boostorg/regex/issues/40#issuecomment-406799721  

You're correct: this is a bug.  Fixed in the commit referenced above.
