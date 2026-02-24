# #356 - Review feedback [Closed]

> Username: vinniefalco  
> Created at: 2020-09-17 19:10:21 UTC  
> Updated at: 2020-09-22 16:55:51 UTC  
> Closed at: 2020-09-22 16:55:50 UTC  
> Url: https://github.com/boostorg/json/issues/356  

> Add reference to RFC 7159 (https://www.rfc-editor.org/info/rfc7159). It allows implementations to set limits on the range and precision of numbers accepted.  
  
This could be in a "Standards Compliance" section in Overview  
  
> 2. The documentation for array object and index mention "Satisfies the  
> requirements of ContiguousContainer, ReversibleContainer, and  
> SequenceContainer" These requirements are never defined or referenced.   
  
These should link to cppreference  
  
> 3. String has two sections "Member Functions": https://master.json.cpp.al/json/ref/boost__json__string.html  
  
The second section is "Static Members", this needs fixing in docca  
  
Implementation-defined limits need to be discussed along with the rationale

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-18 00:34:05 UTC  
> Url: https://github.com/boostorg/json/issues/356#issuecomment-694576070  

named requirements are linked now, and `swap` is a friend so "Member Functions" should not appear twice

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-22 16:55:50 UTC  
> Url: https://github.com/boostorg/json/issues/356#issuecomment-696848631  

filed individually under #376 and #377
