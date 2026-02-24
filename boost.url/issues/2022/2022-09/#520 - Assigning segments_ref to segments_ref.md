# #520 - Assigning segments_ref to segments_ref [Open]

> Username: vinniefalco  
> Created at: 2022-09-09 02:43:12 UTC  
> Updated at: 2022-09-12 12:10:41 UTC  
> Url: https://github.com/boostorg/url/issues/520  

Consider the following:  
```  
url u1( "/index%2Ehtm" );  
url u2( "/path/to/file%2Etxt" );  
segments_ref ps1 = u1.segments();  
segments_ref Type ps2 = u2.segments();  
ps1 = p2;  
```  
  
What should the contents of ps1 be? Well, `operator=` is documented with this effect:  
```  
ps1.assign( ps2.begin(), p2.end() );  
```  
  
Iterating ps2 will produce decoded segments, so ps1 will end up with  
```  
{ "path", "to", "file.txt" }  
```  
  
Note how "file.txt" has the escape removed. This behavior is technically correct according to our specification but is this the right thing to do?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:22:57 UTC  
> Url: https://github.com/boostorg/url/issues/520#issuecomment-1243065778  

@pdimov any idears?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-09-12 07:29:41 UTC  
> Url: https://github.com/boostorg/url/issues/520#issuecomment-1243327743  

Yes it's the right thing to do.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-09-12 07:33:53 UTC  
> Url: https://github.com/boostorg/url/issues/520#issuecomment-1243331850  

Even more interesting is `u1.segments() = u1.segments();`, which should do the same (that is, remove any unnecessary percent encoding.)  
  
Not sure about `u1.segments().back() = u1.segments().back();`. Does this work?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-12 12:10:24 UTC  
> Updated at: 2022-09-12 12:10:41 UTC  
> Url: https://github.com/boostorg/url/issues/520#issuecomment-1243646430  

> Not sure about u1.segments().back() = u1.segments().back();. Does this work?  
  
No, because back() just returns a string. Assignment can't work for elements, since iterators don't return modifiable references to the underlying element.
