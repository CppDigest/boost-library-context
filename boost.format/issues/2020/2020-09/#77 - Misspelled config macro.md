# #77 - Misspelled config macro [Closed]

> Username: jefftrull  
> Created at: 2020-09-04 05:09:02 UTC  
> Updated at: 2022-02-10 12:18:24 UTC  
> Closed at: 2022-02-10 12:18:24 UTC  
> Url: https://github.com/boostorg/format/issues/77  

`format.hpp` refers to a macro `BOOST_NO_LOCALE_ISIDIGIT`, which probably should be `BOOST_NO_LOCALE_ISDIGIT`

---

## Comment 1

> Username: jeking3  
> Created at: 2022-02-10 11:48:57 UTC  
> Url: https://github.com/boostorg/format/issues/77#issuecomment-1034828096  

Interesting to look into it anyway, it's for a Borland C++ compiler workaround for releases before 2006 edition.  
  
https://github.com/boostorg/format/blob/develop/include/boost/format/detail/config_macros.hpp#L40-L46  
  
https://docwiki.embarcadero.com/RADStudio/Sydney/en/Predefined_Macros#C.2B.2B_Compiler_Versions_in_Predefined_Macros
