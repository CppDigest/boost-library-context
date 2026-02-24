# #40 - vcpkg install boost-bloom? [Closed]

> Username: JasonDictos  
> Created at: 2025-07-18 10:59:58 UTC  
> Updated at: 2025-10-15 13:12:25 UTC  
> Closed at: 2025-10-15 13:12:25 UTC  
> Url: https://github.com/boostorg/bloom/issues/40  

Where is this boost-bloom port definition? I can't find it on the master branch of vcpkg.

---

## Comment 1

> Username: cmazakas  
> Created at: 2025-07-18 14:11:18 UTC  
> Url: https://github.com/boostorg/bloom/issues/40#issuecomment-3089607968  

The 1.89 release, which Bloom is shipping in, isn't out until August 13th.  
  
In the interim, you can make your own overlay port using this as a template:  https://github.com/cmazakas/vcpkg-registry/tree/8b73ea0efa0d35b4cdafaff4acc3545a71d81b64/ports/boost-bloom  
  
You'll have to hand edit a good chunk of the fields but hopefully it's somewhat straight-forward. Let me know if you need any clarification on what to edit and how to use an overlay port.

---

## Comment 2

> Username: joaquintides  
> Created at: 2025-10-15 13:12:25 UTC  
> Url: https://github.com/boostorg/bloom/issues/40#issuecomment-3406390291  

It's just been made available:  
  
https://vcpkg.io/en/package/boost-bloom
