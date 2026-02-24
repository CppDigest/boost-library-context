# #164 - eval(string), exec(string) ignore global, local parameters [Closed]

> Username: res2k  
> Created at: 2017-10-18 09:34:33 UTC  
> Updated at: 2017-10-18 18:05:54 UTC  
> Closed at: 2017-10-18 18:05:54 UTC  
> Url: https://github.com/boostorg/python/issues/164  

Commit b2f53e1 added new overloads for `eval()` and the various `exec` functions.  
However, the backwards compatible `eval(string)` and `exec(string)` methods accepting a `boost::python::string` don't pass through the `global` and `local` args, breaking code that relies on them.  
`exec_statement(string)`, `exec_file(string)` are fine.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-10-18 18:05:54 UTC  
> Url: https://github.com/boostorg/python/issues/164#issuecomment-337678307  

Fixed with https://github.com/boostorg/python/commit/b09d80a93e299bdaba29b652010b668b87f1306c
