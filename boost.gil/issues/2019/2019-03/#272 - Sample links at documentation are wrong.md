# #272 - Sample links at documentation are wrong [Open]

> Username: Sairony  
> Created at: 2019-03-28 14:08:42 UTC  
> Updated at: 2020-03-04 18:01:47 UTC  
> Url: https://github.com/boostorg/gil/issues/272  

Scroll down to the bottom of https://www.boost.org/doc/libs/1_69_0/libs/gil/doc/html/index.html to the samples. Clicking any of the links downloads the file instead of opening it up in the browser ( at least on chrome ). Probably this is due to the file extension being .cpp, when in actuality it's a html document.

---

## Comment 1

> Username: codejaeger  
> Created at: 2020-03-03 19:43:05 UTC  
> Updated at: 2020-03-03 19:52:01 UTC  
> Url: https://github.com/boostorg/gil/issues/272#issuecomment-594132498  

@mloskot Wasn't sure if this should have been a separate issue,  
In the GIL design guide , in the [concepts](https://boostorg.github.io/gil/html/design/concepts.html) page , the definition of concept equality_comparable seems to have a small typo it should have the signatures   
bool operator==(T x, **U** y);  
bool operator!=(T x, **U** y) { return !(x==y); }  
instead of   
bool operator==(T x, T y);  
bool operator!=(T x, T y) { return !(x==y); }  
as from the [ConceptsC++ Concept Web](https://web.archive.org/web/20160623190823/http://www.generic-programming.org/languages/conceptcpp/concept_web.php?concept=EqualityComparable).

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-04 18:01:47 UTC  
> Url: https://github.com/boostorg/gil/issues/272#issuecomment-594710979  

@codejaeger Good catch, if you're willing to submit PR, it would be helpful.
