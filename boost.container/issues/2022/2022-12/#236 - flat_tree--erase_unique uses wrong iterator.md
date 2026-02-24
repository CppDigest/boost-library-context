# #236 - flat_tree::erase_unique uses wrong iterator [Closed]

> Username: revorm  
> Created at: 2022-12-06 12:53:11 UTC  
> Updated at: 2023-02-13 09:50:12 UTC  
> Closed at: 2023-02-13 09:04:13 UTC  
> Url: https://github.com/boostorg/container/issues/236  

In flat_tree.hpp on line 1132, erase_unique needs to get a const_iterator from its call to find, otherwise you can run in to trouble resolving the call to erase.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-12-13 23:29:48 UTC  
> Url: https://github.com/boostorg/container/issues/236#issuecomment-1350042090  

What is the problem when resolving the call? An iterator is convertible to a const_iterator.

---

## Comment 2

> Username: revorm  
> Created at: 2022-12-16 16:19:43 UTC  
> Url: https://github.com/boostorg/container/issues/236#issuecomment-1355164149  

For some reason, GCC 11 is unable to resolve if it should call the const_iterator or key_type overload of flat_tree::erase.   
  
It seems like something in my work codebase is triggering this in GCC since it doesn't break with a simple reproducer.  
It seems, however, that in the specific case of erase_unique we always expect to call the const_iterator overload of erase.  
  
Unfortunately I am not able to produce a simplified reproducer so if you feel like that's insufficient evidence of an issue on your end then feel free to close the issue.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2023-02-13 09:21:33 UTC  
> Url: https://github.com/boostorg/container/issues/236#issuecomment-1427603170  

Thanks for the report.

---

## Comment 4

> Username: revorm  
> Created at: 2023-02-13 09:50:12 UTC  
> Url: https://github.com/boostorg/container/issues/236#issuecomment-1427639186  

Thanks for the fix!
