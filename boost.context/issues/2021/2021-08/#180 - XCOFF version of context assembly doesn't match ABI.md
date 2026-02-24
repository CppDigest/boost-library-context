# #180 - XCOFF version of context assembly doesn't match ABI? [Closed]

> Username: NattyNarwhal  
> Created at: 2021-08-05 19:32:59 UTC  
> Updated at: 2021-08-11 19:30:22 UTC  
> Closed at: 2021-08-11 19:29:43 UTC  
> Url: https://github.com/boostorg/context/issues/180  

I'm porting boost::context's XCOFF (you know, AIX and its ABI) assembly for PHP's own fibers (see php/php-src#7338; PHP's own), but I noticed some oddities with the assembly. (This isn't essential because of a `ucontext_t` fallback, but I got curious as to why.)  
  
* The xcoff/ppc64 version of `make_fcontext` truncates values to 32-bit. This seems to be fixable with  
* The xcoff/ppc64 versions of make/jump seem to not be using the AIX ABI (!). Notably, they don't seem aware of function descriptors; the ppc64 _CALL_ELF==1 code in the ELF version handles this, because the AIX ABI is mostly identical to ELFv1. Likewise, jump seems to restore r13 instead of r2 (TOC) instead; IBM's own docs say this is reserved, and the ELF code just restores r2 for v1 and nothing for v2.  
  
These seem so fundamental (and attached to the build system), so having them not work at all is a bit confusing. Especially the fact they're for the IBM and not GNU assembler, which has syntax differences.

---

## Comment 1

> Username: olk  
> Created at: 2021-08-06 05:10:24 UTC  
> Url: https://github.com/boostorg/context/issues/180#issuecomment-894005269  

I tried to implement the XCOF variant without access to a PPC64/AIX system - so I couldn't test it and it's likely that it contains mistakes.  
Could you provide a fix/Pull-request, please?

---

## Comment 2

> Username: NattyNarwhal  
> Created at: 2021-08-06 13:44:53 UTC  
> Url: https://github.com/boostorg/context/issues/180#issuecomment-894271285  

I've fixed some of the issues, but I'm still in the process of figuring out the other ones.

---

## Comment 3

> Username: NattyNarwhal  
> Created at: 2021-08-11 17:58:35 UTC  
> Url: https://github.com/boostorg/context/issues/180#issuecomment-897032691  

Over on the PHP downstream thread, after basically forcing the AIX version to look like the ELFv1 version with IBM assembly syntax, it does appear to work fine. PR to come.  
  
I've only tested 32-bit because the environment I'm working out of only provides 64-bit builds.

---

## Comment 4

> Username: olk  
> Created at: 2021-08-11 19:30:21 UTC  
> Url: https://github.com/boostorg/context/issues/180#issuecomment-897093624  

ty,merged
