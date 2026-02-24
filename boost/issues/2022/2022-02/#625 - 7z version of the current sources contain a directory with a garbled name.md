# #625 - 7z version of the current sources contain a directory with a garbled name [Closed]

> Username: jrmoore  
> Created at: 2022-02-21 12:43:46 UTC  
> Updated at: 2022-02-21 21:45:37 UTC  
> Closed at: 2022-02-21 21:45:37 UTC  
> Url: https://github.com/boostorg/boost/issues/625  

Not important or breaking, but the current source distribution has a directory with UTF-8 characters outside of the usual range that ended up being misinterpreted when archived to 7z.  
  
It's in `libs\wave\test\testwave\testfiles`, the directory is supposed to be named `utf8-test-ßµ™∃`, but inside of the archive it's something like `utf8-test-ÃÂµâ¢â`  
  
Maybe the way that archive is created should be revised, the rest are fine, zip and tar.*. I tried archiving in Windows in case it was a 7-Zip problem, but it seems to work as expected.

---

## Comment 1

> Username: sdarwin  
> Created at: 2022-02-21 18:15:46 UTC  
> Url: https://github.com/boostorg/boost/issues/625#issuecomment-1047130987  

Hi @jrmoore, this might already be fixed, did you try with _latest_ snapshot, being the develop branch of boostorg/boost?  Here are the download links: https://boostorg.jfrog.io/artifactory/main/develop/

---

## Comment 2

> Username: jrmoore  
> Created at: 2022-02-21 21:45:37 UTC  
> Url: https://github.com/boostorg/boost/issues/625#issuecomment-1047249123  

Thanks @sdarwin, the current snapshot is packaged as expected, I'm closing the issue already 😉.
