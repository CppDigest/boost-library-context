# #9 Removed inclusion of the boost/cast.hpp (functions from this library wer... [Merged]

> Username: apolukhin  
> Created at: 2014-06-09 08:56:23 UTC  
> Updated at: 2014-06-13 02:42:46 UTC  
> Merged at: 2014-06-10 16:37:18 UTC  
> Closed at: 2014-06-10 16:37:18 UTC  
> Url: https://github.com/boostorg/gil/pull/9  

...e not used)  
  
This drops dependency to the Conversion Library and Numeric Cast.  
  
Tested on GCC. Tests do pass.

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-06-10 16:41:05 UTC  
> Url: https://github.com/boostorg/gil/pull/9#issuecomment-45639156  

Hmm. I spoke too soon. The files do call `boost::numeric_cast`, so I'll have to revert that commit.

---

## Comment 2

> Username: chhenning  
> Created_at: 2014-06-10 17:03:08 UTC  
> Url: https://github.com/boostorg/gil/pull/9#issuecomment-45642090  

No problem. Thanks for working on gil.  
  
On Tue, Jun 10, 2014 at 12:41 PM, Peter Dimov notifications@github.com  
wrote:  
  
> Hmm. I spoke too soon. The files do call boost::numeric_cast, so I'll  
> have to revert that commit.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/gil/pull/9#issuecomment-45639156.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2014-06-10 17:11:40 UTC  
> Url: https://github.com/boostorg/gil/pull/9#issuecomment-45643182  

`grep` showed that numeric_cast is used only in a single file. Added numeric_cast include file and made another pull request: https://github.com/boostorg/gil/pull/11

---
