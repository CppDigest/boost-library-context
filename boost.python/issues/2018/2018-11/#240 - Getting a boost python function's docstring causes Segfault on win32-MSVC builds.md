# #240 - Getting a boost python function's docstring causes Segfault on win32/MSVC builds. [Closed]

> Username: ricrogz  
> Created at: 2018-11-06 13:59:44 UTC  
> Updated at: 2018-11-26 14:57:16 UTC  
> Closed at: 2018-11-26 14:57:15 UTC  
> Url: https://github.com/boostorg/python/issues/240  

This bug triggers when accessing the `__doc__` property of a boost python wrapped function. It was detected on importing the `rdkit.Chem.Lipinski` module of the RDKit (see line 95 [here](https://github.com/rdkit/rdkit/blob/master/rdkit/Chem/Lipinski.py); `rdMolDescriptors` is boost python wrapped). The problem was detected on Windows 7 x64, having built both boost and RDKit with VS2017, and has been reproduced on a different machine (also Windows 7 x64) in the conda environment. `gcc` builds on linux do not seem to exhibit this bug.  
  
The segfault apparently happens because, `s.basename` for the function's return type has vanished and contains a `nullptr` when evaluated inside `function_doc_signature_generator::py_type_str` ([https://github.com/boostorg/python/blob/develop/src/object/function_doc_signature.cpp](https://github.com/boostorg/python/blob/develop/src/object/function_doc_signature.cpp), line 119).

---

## Comment 1

> Username: mnordie  
> Created at: 2018-11-22 13:43:26 UTC  
> Url: https://github.com/boostorg/python/issues/240#issuecomment-441034813  

Possible duplicate of #207?  
We've used PR #208 to correct this issue. It's not in the 1.69 beta though.

---

## Comment 2

> Username: ricrogz  
> Created at: 2018-11-26 14:57:15 UTC  
> Url: https://github.com/boostorg/python/issues/240#issuecomment-441667591  

Hi @mnordie, actually, yes, it seems to be a duplicate. I tried #208, and it solves the problem. Thanks, and sorry for the trouble!
