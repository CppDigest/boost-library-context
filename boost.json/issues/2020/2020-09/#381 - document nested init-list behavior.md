# #381 - document nested init-list behavior [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:02:53 UTC  
> Updated at: 2022-05-25 12:05:05 UTC  
> Closed at: 2022-05-25 12:05:05 UTC  
> Url: https://github.com/boostorg/json/issues/381  

Additionally, I doesn't mention how nested initializer_list how are treated. Most importantly it should show or link to some examples on how the initializer_list are converted to objects, arrays, and non structured values because some developers (if not most) might not immediately realize from just looking at the reference documentation how nested initializer_list are parsed. Maybe also reference to the initializer_list constructors of array and object.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-05-25 09:51:27 UTC  
> Url: https://github.com/boostorg/json/issues/381#issuecomment-1137034397  

I think, the [current documentation](https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/dom/initializer_lists.html) cover all the topics described  in this issue.
