# #93 - Overhaul of axis concept [Closed]

> Username: HDembinski  
> Created at: 2018-09-21 08:11:59 UTC  
> Updated at: 2019-03-05 00:52:46 UTC  
> Closed at: 2019-03-05 00:52:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/93  

Concepts are called AxisType instead of axis_type.  
  
* concepts should define usage and behavior, not implementation  
* make label and iterator_mixin optional  
* bin layout must be part of the specs  
* Add BinType concept? (not needed, I think)  
* Add ValueType concept? (not needed, I think)

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-10-10 13:36:51 UTC  
> Updated at: 2018-10-10 13:38:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/93#issuecomment-428574336  

Style examples in boost:  
https://www.boost.org/doc/libs/1_68_0/libs/hof/doc/html/doc/src/concepts.html  
https://www.boost.org/doc/libs/1_62_0/libs/graph/doc/graph_concepts.html

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-10-10 13:45:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/93#issuecomment-428577566  

concept page example from cppreference.com  
https://en.cppreference.com/w/cpp/named_req/InputIterator

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-10-10 13:59:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/93#issuecomment-428582633  

Steven's comments:  
>  Concept names use CamelCase, by convention.  
  
> It doesn't seem very sane to have the value_type be a reference. Is there any reason for this other than the fact that you specify the exact signature of axis_type::index?  
  
We can probably avoid specifying value_type altogether.  
  
>  I don't understand the difference between the two overloads of storage_type::add.  
  
May be superfluous once adaptive_storage looses weight support.

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-12-17 15:08:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/93#issuecomment-447877796  

from #103:  
Consider using a tabular layout similar to that of the C++ standard (or  
cppreference.com for that matter) on the Concepts page.
