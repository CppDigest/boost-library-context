# #7 Fix validation error in explicit-failures-markup.xml [Closed]

> Username: danieljames  
> Created at: 2018-01-11 11:51:13 UTC  
> Updated at: 2018-01-14 15:35:22 UTC  
> Closed at: 2018-01-12 15:56:56 UTC  
> Url: https://github.com/boostorg/contract/pull/7  

Fix this validation error:  
  
    $ xmllint --schema ~/boost/develop/status/explicit-failures.xsd explicit-failures-markup.xml  
    explicit-failures-markup.xml:8: parser error : XML declaration allowed only at the start of the document  
    <?xml version="1.0" encoding="utf-8"?>  
    `     ^

---

## Comment 1

> Username: lcaminiti  
> Created_at: 2018-01-12 15:56:56 UTC  
> Url: https://github.com/boostorg/contract/pull/7#issuecomment-357276570  

I fixed by removing the copyright comment from this file. This file will be merged into the main explicit failure markup file and if I put the copyright comment here, it will be repeated in the main file. That done for each lib, will clutter the main file with many copyright comments.

---

## Comment 2

> Username: danieljames  
> Created_at: 2018-01-12 18:55:39 UTC  
> Url: https://github.com/boostorg/contract/pull/7#issuecomment-357323457  

It won't, the script extracts the 'library' tags and their contents, so it ignores the copyright comment. If you look at the develop branch for unordered, functional, container_hash and quickbook they all have a copyright comment but none of them are copied into the main file.

---

## Comment 3

> Username: lcaminiti  
> Created_at: 2018-01-14 15:35:22 UTC  
> Url: https://github.com/boostorg/contract/pull/7#issuecomment-357519820  

I see. I added the copyright and licence comment to the file as suggested by this pull request.

---
