# #10 - process for rebuilding all effective docs when boostbook changes [Closed]

> Username: rbbeeston  
> Created at: 2024-09-13 18:49:15 UTC  
> Updated at: 2024-12-10 20:03:18 UTC  
> Closed at: 2024-12-10 20:03:16 UTC  
> Url: https://github.com/boostorg/boostlook/issues/10  

When someone renders asciidoctor docs, the boostlook.css file contents need to be inserted. This is accomplished by passing the right command line argument to the asciidoc tool, as shown here:  
https://github.com/vinniefalco/mp11/blob/develop/doc/Jamfile#L13  
  
When someone renders antora docs, the same thing should happen. The boostlook.css file contents should be inserted into the resulting HTML  
  
The website injects the header when it serves documentation. And we should modify the asciidoc and antora build process so that the resulting HTML has a hint to where the website should insert the header

---

## Comment 1

> Username: julioest  
> Created at: 2024-12-10 03:25:26 UTC  
> Url: https://github.com/boostorg/boostlook/issues/10#issuecomment-2530170937  

@rbbeeston Can this be closed now that we have our boostlook actions workflow updating v2 and v2-docs?

---

## Comment 2

> Username: rbbeeston  
> Created at: 2024-12-10 20:03:16 UTC  
> Url: https://github.com/boostorg/boostlook/issues/10#issuecomment-2532750801  

already resolved
