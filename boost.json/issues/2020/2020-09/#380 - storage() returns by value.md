# #380 - storage() returns by value [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 16:59:57 UTC  
> Updated at: 2020-09-26 02:59:19 UTC  
> Closed at: 2020-09-26 02:59:19 UTC  
> Url: https://github.com/boostorg/json/issues/380  

e.g. `storage_ptr value::storage() const noexcept;`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-22 17:00:45 UTC  
> Url: https://github.com/boostorg/json/issues/380#issuecomment-696851307  

>it's fine in principle to return by const ref if you really want v.storage(); to not do anything, but the documentation should show a return by value

---

## Comment 2

> Username: maximilianriemensberger  
> Created at: 2020-09-22 19:57:54 UTC  
> Url: https://github.com/boostorg/json/issues/380#issuecomment-696946476  

Returning a const reference to a data member of your class in a simple accessor function is usually fine and really not uncommon. Classes like `std::optional` and others do it as well.  It's also no different from `auto const& an_int = std::vector<int>{1,2,3}.back();`. Yes, it dangles. But whoever wrote that purposefully decided to take temporary stuff by reference.  
  
Also the documentation should show and say precisely what the function signature is, regardless of reference or value return, because you want to be honest to the readers of the documentation. You might want to hide what it is (`implementation defined`, I wouldn't know why). But IMO, you should not show an apple and then return apple juice.
