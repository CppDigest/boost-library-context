# #504 - Custom Fields [Closed]

> Username: vinniefalco  
> Created at: 2017-06-17 16:50:03 UTC  
> Updated at: 2022-06-16 16:32:54 UTC  
> Closed at: 2022-06-16 16:32:53 UTC  
> Url: https://github.com/boostorg/beast/issues/504  

To make sure the **Fields** concept is well specified, there should be a couple of example implementations of **Fields** in the test or example dirs.

---

## Comment 1

> Username: gyfscx  
> Created at: 2017-11-07 05:05:44 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-342376856  

i don't found it, where the file for custom field?thanks a lots.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-07 15:17:00 UTC  
> Updated at: 2017-11-07 15:17:07 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-342514206  

>where the file for custom field?  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/concepts/Fields.html

---

## Comment 3

> Username: terrywh  
> Created at: 2018-06-17 09:05:40 UTC  
> Updated at: 2018-06-17 09:39:45 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-397865038  

I'm trying to implement a custom fields container. There is one concept description about `FieldsWriter`:  
  
* `F` denotes a __Fields__ where  
      `std::is_same<W, F::reader>::value == true`.  
  
but i don't see any `reader` defined in `http::fields`. So what is that  `F::reader`? Should I define a `reader` in `Fields`?   
  
also I notice in default `parser::on_field_impl` calls to `Fields::insert` is present, should that also be in the concept ? or i just have to implement a `parser` as well ?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-06-17 12:37:21 UTC  
> Updated at: 2018-06-17 12:37:43 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-397876095  

> i don't see any reader defined in http::fields  
  
That should say `F::writer`, I'll fix the docs  
  
> i just have to implement a `parser` as well ?  
  
Yes, and you need to derive your parser from `basic_parser` for it to work with the HTTP stream algorithms.  
  
I am very interested to see how this goes, please keep me up to date so I can address any more issues if they come up!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-04-19 02:08:12 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-484745146  

Is anyone using this custom Fields feature? I am very likely going to remove it since it hasn't worked out the way I thought it would.

---

## Comment 6

> Username: feverzsj  
> Created at: 2020-10-09 17:08:51 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-706298840  

I tried to derive from http::fields, but the parser only accepts the original fields

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-10-09 17:13:04 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-706300801  

Yes the parser only uses the original fields

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-06-16 16:32:53 UTC  
> Url: https://github.com/boostorg/beast/issues/504#issuecomment-1157883289  

Fields concept was a mistake
