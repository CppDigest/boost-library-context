# #50 - PROTECTED visibility should be listed on symbols since they are exported [Closed]

> Username: slnj  
> Created at: 2021-04-23 00:28:01 UTC  
> Updated at: 2021-05-02 10:31:44 UTC  
> Closed at: 2021-05-02 10:29:43 UTC  
> Url: https://github.com/boostorg/dll/issues/50  

We are using dll boost project to access shared libraries symbols on Linux EL7 (ELF)  
We notice that we symbols with PROTECTED visibility attribute are not listed on library_info symbols.  
Protected symbols are exported and can be accessed when loading shared libraries using dlopen/dlsym, so I understand they should be included on symbols list retrieved using library_info.  
  
We try changing is_visible method on dll/detail/elf_info.hpp, and changing the condition to:  
return ((sym.st_other & 0x03) == STV_DEFAULT_ || (sym.st_other & 0x03) == STV_PROTECTED_ ) && (sym.st_info >> 4) != STB_LOCAL_ && !!sym.st_size;  
and it seems to work.

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-05-02 10:31:43 UTC  
> Url: https://github.com/boostorg/dll/issues/50#issuecomment-830786853  

Fixed in b053729bfba895808f7ce1c0722fee0cda813a93  
  
Many thanks for the bug report and for the proposed fix!
