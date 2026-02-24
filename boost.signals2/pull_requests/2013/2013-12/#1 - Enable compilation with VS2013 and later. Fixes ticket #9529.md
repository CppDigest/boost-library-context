# #1 Enable compilation with VS2013 and later. Fixes ticket #9529 [Merged]

> Username: DanielaE  
> Created at: 2013-12-27 17:48:05 UTC  
> Updated at: 2014-06-17 17:59:05 UTC  
> Merged at: 2014-01-01 16:07:39 UTC  
> Closed at: 2014-01-01 16:07:39 UTC  
> Url: https://github.com/boostorg/signals2/pull/1  

Instead of adding partially specialized class template 'slot' with dependent template argument 'SlotFunction' which doesn't work with Visual Studio compilers, get around this problem by introducing class template 'slot_impl' - much like in the "faux-variadic" code path used by compilers which don't implement variadic templates.  
  
As a by-product, make library level-4-warning-clean.  
  
Signed-off-by: Daniela Engert dani@ngrt.de

---
