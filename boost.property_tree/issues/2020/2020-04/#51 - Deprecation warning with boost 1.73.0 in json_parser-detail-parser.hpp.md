# #51 - Deprecation warning with boost 1.73.0 in json_parser/detail/parser.hpp [Closed]

> Username: rogerorr  
> Created at: 2020-04-30 14:07:54 UTC  
> Updated at: 2021-12-04 08:33:56 UTC  
> Closed at: 2020-05-15 09:54:42 UTC  
> Url: https://github.com/boostorg/property_tree/issues/51  

`json_parser/detail/parser.hpp` includes `<boost/bind.hpp>` which causes a deprecation warning with boost 1.73.0.  
  
```  
..\boost\boost/bind.hpp(41): note: The practice of declaring the Bind placeholders (_1, _2, ...)  
in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using  
namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to  
retain the current behavior.  
```  
  
This could for example be resolved by using `#include <boost/bind/bind.hpp>` and adding a `using namespace boost::placeholders` in the methods that use `boost::bind` with `_1`, etc.

---

## Comment 1

> Username: CornedBee  
> Created at: 2020-05-15 09:54:42 UTC  
> Url: https://github.com/boostorg/property_tree/issues/51#issuecomment-629145125  

Should be closed by #50

---

## Comment 2

> Username: xhronos  
> Created at: 2020-10-21 13:07:40 UTC  
> Url: https://github.com/boostorg/property_tree/issues/51#issuecomment-713555888  

That fix did only go into the development branch but cannot be found in boost release 1.74.  
  
https://github.com/boostorg/property_tree/blob/bdfe275d172ac30bc5e89a6375a5a64dea20b3c0/include/boost/property_tree/json_parser/detail/parser.hpp#L217

---

## Comment 3

> Username: Kaldie  
> Created at: 2021-03-08 18:56:22 UTC  
> Url: https://github.com/boostorg/property_tree/issues/51#issuecomment-792992042  

Also did not go into 1.75.0 release?

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-03-08 19:45:10 UTC  
> Url: https://github.com/boostorg/property_tree/issues/51#issuecomment-793024523  

I've just picked up maintenance of boost.property_tree. I'm still finding my way around where we are.  
I'll do what I can to get it into 1.76

---

## Comment 5

> Username: 3togo  
> Created at: 2021-12-04 08:33:56 UTC  
> Url: https://github.com/boostorg/property_tree/issues/51#issuecomment-985991484  

I confirm that this bug could be fixed by changing alll the <boost/bind.hpp> to <boost/bind/bind.hpp>  
  
```  
#! /bin/bash  
  
cd /usr/include/boost  
grep -r "<boost/bind.hpp>"|cut -d ' ' -f1|cut -d ":" -f1|xargs sudo sed -i 's|<boost/bind.hpp>|<boost/bind/bind.hpp>|g' {} \;  
```
