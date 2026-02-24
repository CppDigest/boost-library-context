# #6 Make copyright footer generation simpler and more flexible. [Merged]

> Username: vprus  
> Created at: 2014-12-08 07:41:53 UTC  
> Updated at: 2023-02-26 14:28:27 UTC  
> Merged at: 2023-02-26 14:28:26 UTC  
> Closed at: 2023-02-26 14:28:26 UTC  
> Url: https://github.com/boostorg/boostbook/pull/6  

The footer template has code to generate revision number,  
which was never updated to git and therefore generates no text,  
but still generates a somewhat complex table structure. This  
commit removes code for version numbers, and makes output a simple  
div.  
  
I've also added a hook to use pick content of whatever paragraph  
with role of 'copyright' the document has, since right now we  
pick copyright only from 'bookinfo' element, and Docbook renders  
bookinfo element right at the start, and for Boost.Build documentation  
I want documentation to start with some hints how to start, not  
with a copyright statement.

---
