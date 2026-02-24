# #66 Update for Oracle 12.4 compiler [Closed]

> Username: jzmaddock  
> Created at: 2015-02-26 13:29:39 UTC  
> Updated at: 2015-02-26 18:24:47 UTC  
> Closed at: 2015-02-26 17:06:42 UTC  
> Url: https://github.com/boostorg/boost/pull/66  

We need to update instructions for the latest Oracle/Sun compilers, note however that these instructions (at least the stdlib= part) are dependent on https://github.com/boostorg/build/pull/60

---

## Comment 1

> Username: danieljames  
> Created_at: 2015-02-26 16:21:20 UTC  
> Url: https://github.com/boostorg/boost/pull/66#issuecomment-76208207  

You need to update the rst file or your changes will get overwritten next time it's rebuilt. Also, the pull request needs to be made against develop.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-02-26 17:06:15 UTC  
> Url: https://github.com/boostorg/boost/pull/66#issuecomment-76219042  

On 26/02/2015 16:21, Daniel James wrote:  
  
> You need to update the rst file or your changes will get overwritten   
> next time it's rebuilt. Also, the pull request needs to be made   
> against develop.  
  
That's what I thought, _but_, the html file is way newer than the rst   
file, and the rst file actually has no mention of the sun toolset at   
all:  unix-variants.rst last updated 4 years ago, unix-variants.html 4   
months ago.  So it seems like the rst files should be deleted?  
  
On reflection though.... I'll close this one, and submit one against   
develop which doesn't reference the as-yet-not-in-develop changes to   
sun.jam.

---

## Comment 3

> Username: danieljames  
> Created_at: 2015-02-26 18:24:47 UTC  
> Url: https://github.com/boostorg/boost/pull/66#issuecomment-76235170  

The relevant source to edit is in `more/getting_started/detail/build-from-source-head.rst`.

---
