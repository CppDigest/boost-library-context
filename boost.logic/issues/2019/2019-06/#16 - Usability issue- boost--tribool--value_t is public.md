# #16 - Usability issue: boost::tribool::value_t  is public [Open]

> Username: fekir  
> Created at: 2019-06-28 06:54:40 UTC  
> Updated at: 2019-07-02 19:21:39 UTC  
> Url: https://github.com/boostorg/logic/issues/16  

The tribool interface is not very user-friendly, I saw more than once following error:  
  
  
assignment:  
````  
boost::tribool v1 = boost::tribool::false_value;  
boost::tribool v2 = boost::tribool::true_value;  
boost::tribool v3 = boost::tribool::indeterminate_value;  
````  
  
comparison:  
  
````  
v1 == boost::tribool::false_value;  
v2 == boost::tribool::true_value;  
v3 == boost::tribool::indeterminate_value;  
````  
  
The issue is frequent because of auto completion.  
Since `operator==`,  `operator safe_bool`  and the function `indeterminate` it is possible to query the status of `boost::tribool`, I do not think there are good reasons for the `value_t` to be public.  
  
If `boost::tribool::value_t` is changed to private, some function like `indeterminate` and `operator!` needs to be made friends, but otherwise there should be no other issues.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-07-02 18:49:03 UTC  
> Url: https://github.com/boostorg/logic/issues/16#issuecomment-507800661  

I presume that the trailing `_value` is a typo?

---

## Comment 2

> Username: pdimov  
> Created at: 2019-07-02 18:49:41 UTC  
> Url: https://github.com/boostorg/logic/issues/16#issuecomment-507800868  

In  
```  
boost::tribool v3 = boost::tribool::indeterminate_value _value;  
```  
and  
```  
v3 == boost::tribool::indeterminate_value _value;  
```  
I mean.

---

## Comment 3

> Username: fekir  
> Created at: 2019-07-02 19:20:54 UTC  
> Updated at: 2019-07-02 19:21:39 UTC  
> Url: https://github.com/boostorg/logic/issues/16#issuecomment-507811138  

Yes, sorry, should have been  
  
````  
boost::tribool v3 = boost::tribool::indeterminate_value;  
v3 == boost::tribool::indeterminate_value;  
````  
  
I've updated the first description, thanks!
