# #128 Mark 32-bit GNU assembly files targeting Windows as safeseh compatible [Merged]

> Username: ericastor  
> Created at: 2019-11-28 03:33:03 UTC  
> Updated at: 2019-11-28 06:37:14 UTC  
> Merged at: 2019-11-28 06:37:10 UTC  
> Closed at: 2019-11-28 06:37:10 UTC  
> Url: https://github.com/boostorg/context/pull/128  

We set the LSB of the magic symbol @feat.00 to 1. This symbol is used for compiler-linker communication, and the LSB expresses that an object file has opted into "safeseh"; any SEH handlers used in this file must be listed in the .sxdata section.  
  
Since we don't register any SEH handlers in these files, this is trivially satisfied.  
  
Reference: [the PE-COFF specification](https://docs.microsoft.com/en-us/windows/win32/debug/pe-format#the-sxdata-section).

---

## Comment 1

> Username: olk  
> Created_at: 2019-11-28 06:37:14 UTC  
> Url: https://github.com/boostorg/context/pull/128#issuecomment-559362083  

ty

---
