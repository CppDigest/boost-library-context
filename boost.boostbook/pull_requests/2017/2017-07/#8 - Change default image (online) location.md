# #8 Change default image (online) location. [Merged]

> Username: jzmaddock  
> Created at: 2017-07-03 18:27:49 UTC  
> Updated at: 2017-07-05 20:11:20 UTC  
> Merged at: 2017-07-05 18:09:00 UTC  
> Closed at: 2017-07-05 18:09:00 UTC  
> Url: https://github.com/boostorg/boostbook/pull/8  

I get HTTP 303 errors when retrieving images from the old location (browser is fine, but Java chokes while building the PDF's).  I couldn't find a git location that didn't also give 303 errors, so this seems to be the next best alternative.

---

## Comment 1

> Username: danieljames  
> Created_at: 2017-07-03 20:37:58 UTC  
> Url: https://github.com/boostorg/boostbook/pull/8#issuecomment-312729502  

FWIW 303 isn't an error code, 3xx codes are for redirects, 4xx and 5xx codes are errors. 303 is a bit unusual though, I wonder if 302 would better as it's more common and possibly more apt..  
  
But either way, 'doc/libs/release' links also return a 303 status code, so I don't see why they'd be better. Maybe the java doesn't like redirect chains? Using 'doc/lib/develop' would probably be closer to using trunk links in subversion, and wouldn't involve redirects.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-07-05 17:12:55 UTC  
> Url: https://github.com/boostorg/boostbook/pull/8#issuecomment-313167446  

Confirmed that "develop" also worked, changed the PR accordingly.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-07-05 20:11:20 UTC  
> Url: https://github.com/boostorg/boostbook/pull/8#issuecomment-313213080  

On 05/07/2017 19:09, Daniel James wrote:  
>  
> Merged #8 <https://github.com/boostorg/boostbook/pull/8>.  
>  
Thanks!  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
