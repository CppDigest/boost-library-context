# #5 set xsltproc maxdepth [Merged]

> Username: sdarwin  
> Created at: 2020-07-03 15:17:12 UTC  
> Updated at: 2020-07-03 21:06:13 UTC  
> Merged at: 2020-07-03 21:06:13 UTC  
> Closed at: 2020-07-03 21:06:13 UTC  
> Url: https://github.com/boostorg/vmd/pull/5  

If you build the entire boost release, which includes vmd as one of the libraries, on Ubuntu 20.04, the following error appears:  
  
  
_xsltApplySequenceConstructor: A potential infinite template recursion was detected.  
You can adjust xsltMaxDepth (--maxdepth) in order to raise the maximum number of nested template calls and variables/params (currently set to 3000)._  
  
Their suggestion is to adjust --maxdepth.  That change does seem to solve it.  
  
More info:  
  
Ubuntu 18 has these libraries:  
  
ii  libxslt1.1:amd64                 1.1.29-5ubuntu0.2                   amd64        XSLT 1.0 processing library - runtime library  
ii  xsltproc                         1.1.29-5ubuntu0.2                   amd64        XSLT 1.0 command line processor  
  
Ubuntu 20:  
  
ii  libxslt1.1:amd64                1.1.34-4                          amd64        XSLT 1.0 processing library - runtime library  
ii  xsltproc                        1.1.34-4                          amd64        XSLT 1.0 command line processor  
  
The error detection was added shortly after version 1.1.29 , in libxslt1.1

---
