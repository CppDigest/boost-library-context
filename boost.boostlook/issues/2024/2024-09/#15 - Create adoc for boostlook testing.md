# #15 - Create adoc for boostlook testing [Closed]

> Username: rbbeeston  
> Created at: 2024-09-23 18:59:56 UTC  
> Updated at: 2024-09-24 18:53:32 UTC  
> Closed at: 2024-09-24 18:53:31 UTC  
> Url: https://github.com/boostorg/boostlook/issues/15  

Boostlook needs a CI setup where on PR or push to master/develop it renders the sample.adoc into HTML and publishes it, so at any time we can look at the master or develop rendering and know what the latest style looks like.  
`../../../b2 cxxstd=20 toolset=gcc-13 define=BOOST_CHARCONV_RUN_BENCHMARKS STL_benchmark linkflags="-lfmt" -a release .`  
needs to  go into a code block and we want to see that in the sample.adoc  
  
This sample should include all options used in the adoc.

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-09-23 20:14:20 UTC  
> Url: https://github.com/boostorg/boostlook/issues/15#issuecomment-2369282359  

@rbbeeston sample.adoc is input or output?   Could you provide a `preview/build.sh` script that builds what you would like to see?  What is the input, and what is the output?  Everything could go in a preview/ folder.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-09-23 20:18:34 UTC  
> Updated at: 2024-09-23 20:18:47 UTC  
> Url: https://github.com/boostorg/boostlook/issues/15#issuecomment-2369293580  

if we want to do this right, there should be a Jamfile which imports the boostlook module and invokes the asciidoc tool on the sample.adoc file.

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-09-24 18:53:31 UTC  
> Url: https://github.com/boostorg/boostlook/issues/15#issuecomment-2372062550  

develop.boostlook.cpp.al  
master.boostlook.cpp.al
