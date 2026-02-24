# #463 - Add support for value with default [Closed]

> Username: madmongo1  
> Created at: 2020-11-11 16:21:55 UTC  
> Updated at: 2021-03-04 05:31:23 UTC  
> Closed at: 2021-03-04 05:31:23 UTC  
> Url: https://github.com/boostorg/json/issues/463  

It's often useful in application code to be able to query a JSON object for a value and use a default if not present.  
  
for example:  
  
Currently to check for the presence of an element one objects deep, defaulting if not present or the correct type:  
```  
// unnecessarily ends up becoming a named function or a lambda  
json::string test(json::value const& v)  
{  
  if(auto o1 = v.is_object())  
    if(auto vfoo = o1->if_contains("foo"))  
      if(auto sfoo = vfoo->is_string())  
        return *sfoo;  
  return {};  
}  
  
...  
  
// all I wanted was foo or nothing  
auto foo = test(v);  
```  
  
Which feels cumbersome when writing application code, as:  
a) It's a lot of typing and,  
b) the complexity masks intent.  
  
It might be easier on regular users to allow:  
  
```  
auto foo = v.object_or({}).at_or({}).string_or("");    
  
// the default arguments could themselves be defaulted  
foo = v.object_or().at_or().string_or();  
```  
  
or perhaps even more readable (some access wrapper name bikeshedding):  
  
```  
// as_string takes an optional parameter which defaults to an empty string  
// on a loosely wrapped value it would be guaranteed not to fail  
  
auto foo = loose(v)["foo"].as_string();    
  
//                    wrapper    object index     array index object index  never fails  
//                       |                  |         |       |             |  
auto hostname = ez_access(config_json)["connections"][n]["hostname"].string_or("localhost");  
```

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-11-11 18:51:41 UTC  
> Url: https://github.com/boostorg/json/issues/463#issuecomment-725598155  

This doesn't sound half-bad... @vinniefalco ?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-11-11 19:28:20 UTC  
> Url: https://github.com/boostorg/json/issues/463#issuecomment-725616072  

`ez_access` is already in the examples, with 2 different syntax options:  
https://github.com/boostorg/json/blob/develop/example/path.cpp#L49  
https://github.com/boostorg/json/blob/develop/example/proxy.cpp#L61

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-11-12 06:54:47 UTC  
> Url: https://github.com/boostorg/json/issues/463#issuecomment-725879558  

There's a small difference. `proxy` mutates the json. I was thinking of something that would work on const values as well.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-11-12 07:14:42 UTC  
> Updated at: 2020-11-12 07:15:16 UTC  
> Url: https://github.com/boostorg/json/issues/463#issuecomment-725888378  

You can make a const version of proxy, e.g. `cproxy`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-03-04 05:31:23 UTC  
> Url: https://github.com/boostorg/json/issues/463#issuecomment-790304758  

It looks like this is addressed with the examples. If this is not sufficient please feel free to open a new issue with a description of the feature. Thanks!
