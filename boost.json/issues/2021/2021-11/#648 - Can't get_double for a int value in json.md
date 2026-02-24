# #648 - Can't get_double for a int value in json? [Closed]

> Username: kilasuelika  
> Created at: 2021-11-06 14:28:25 UTC  
> Updated at: 2024-02-09 00:36:38 UTC  
> Closed at: 2021-11-06 14:33:49 UTC  
> Url: https://github.com/boostorg/json/issues/648  

For a json like:  
```  
{  
 "x": 5  
}  
```  
After read it, then convert it to a double will result in exception:  
```  
json["x"].as_double(); //exception  
json["x"].get_double(); //no exception, but incorrect value  
```  
The problem is that when read a json file. 0 and other integer will default to be integer type in boost.json. There is no way to get them as double except first convert to int and then cast to double. But it's tedious as there may be many values need read.

---

## Comment 1

> Username: kilasuelika  
> Created at: 2021-11-06 14:33:49 UTC  
> Url: https://github.com/boostorg/json/issues/648#issuecomment-962460399  

Use   
```  
value_to<double>(json["x"]);  
```  
is correct.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-11-06 15:16:47 UTC  
> Url: https://github.com/boostorg/json/issues/648#issuecomment-962466227  

Or you can use `value::to_number<double>()`:  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__value/to_number/overload2.html  
e.g.  
```  
json::value jv = 3.0;  
  
int i = jv.to_number< int >();  
```

---

## Comment 3

> Username: teathinker  
> Created at: 2024-02-07 21:04:06 UTC  
> Url: https://github.com/boostorg/json/issues/648#issuecomment-1932895072  

this is really an unexpected behavior and should be fixed instead of closed.

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-02-08 12:43:39 UTC  
> Updated at: 2024-02-08 12:44:00 UTC  
> Url: https://github.com/boostorg/json/issues/648#issuecomment-1934040906  

`get_double` and `as_double` give a `double` if it's _stored there_, not if it the stored value is convertible to double. This is documented. It wouldn't even make sense to do the conversion, since both return a reference.  For `as_double` there is even an additional note (https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/ref/boost__json__value/as_double/overload2.html), that what you expected to happen will not happen, and is not supposed to happen, because the function has a different purpose. It also mentions that the function you want is `value::to_number`, which @vinniefalco has suggested above. This is not a bug, this is the intended behaviour, and it is documented.  
  
If `value::to_number` for some reason does not work for you, please explain why.

---

## Comment 5

> Username: teathinker  
> Created at: 2024-02-09 00:36:37 UTC  
> Url: https://github.com/boostorg/json/issues/648#issuecomment-1935148622  

> `get_double` and `as_double` give a `double` if it's _stored there_, not if it the stored value is convertible to double. This is documented. It wouldn't even make sense to do the conversion, since both return a reference. For `as_double` there is even an additional note (https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/ref/boost__json__value/as_double/overload2.html), that what you expected to happen will not happen, and is not supposed to happen, because the function has a different purpose. It also mentions that the function you want is `value::to_number`, which @vinniefalco has suggested above. This is not a bug, this is the intended behaviour, and it is documented.  
>   
> If `value::to_number` for some reason does not work for you, please explain why.  
  
Thanks for the explanation. I totally understand if it's defined this way, as mentioned in doc. What I was trying to say, was that usually people treated int as a parsable value to double.  
Checking if a value has decimal or not isn't a typical feature of a parsing library. Maybe using a name like as_decimal_double() make the exception more expected. Thanks.
