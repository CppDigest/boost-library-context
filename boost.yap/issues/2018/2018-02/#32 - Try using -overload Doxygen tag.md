# #32 - Try using \overload Doxygen tag [Closed]

> Username: tzlaine  
> Created at: 2018-02-14 21:36:08 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-16 01:18:56 UTC  
> Url: https://github.com/boostorg/yap/issues/32  

To resolve this:  
  
expression.hpp:  
11-13:  \note Due to a limitation of Doxygen, each of the  
        <code>value()</code>, <code>left()</code>, <code>right()</code>, and  
        operator overloads listed here is a stand-in for three member  
   Did you try \overload?  That sometimes helps.

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-02-16 01:18:56 UTC  
> Url: https://github.com/boostorg/yap/issues/32#issuecomment-366117424  

This did not help.
