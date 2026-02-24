# #15 - Missing documentation build [Closed]

> Username: Kojoley  
> Created at: 2019-03-20 20:10:26 UTC  
> Updated at: 2019-03-21 20:06:39 UTC  
> Closed at: 2019-03-21 19:09:42 UTC  
> Url: https://github.com/boostorg/qvm/issues/15  

The documentation is missing for Boost 1.70 Beta1 https://www.boost.org/doc/libs/1_70_0_beta1/libs/qvm/ and develop https://www.boost.org/doc/libs/develop/libs/qvm/  
  
`__boost_check_library__`  
```  
libs/qvm: error: file not found; Did not find [project-root]/index.html file. The file is required for all libraries. Redirection to HTML documentation. <<org-doc-redir>>  
libs/qvm: error: directory not found; Missing [project-root]/doc directory. The [project-root]/doc directory is required for all libraries. Sources to build with and built documentation for the library. If the library needs to build documentation from non-HTML files this location must be buildable with Boost Build. <<org-doc-dir>>  
```

---

## Comment 1

> Username: zajo  
> Created at: 2019-03-21 19:09:42 UTC  
> Url: https://github.com/boostorg/qvm/issues/15#issuecomment-475364855  

Thanks this is now fixed.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-21 20:06:38 UTC  
> Url: https://github.com/boostorg/qvm/issues/15#issuecomment-475382696  

The redirection point to a wrong location. I have opened #16 for fixing it.
