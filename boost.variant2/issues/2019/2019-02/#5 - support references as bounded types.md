# #5 - support references as bounded types? [Open]

> Username: HDembinski  
> Created at: 2019-02-17 12:18:13 UTC  
> Updated at: 2019-06-03 18:02:47 UTC  
> Url: https://github.com/boostorg/variant2/issues/5  

variant2 cannot have references as bounded types, https://godbolt.org/z/mnP-CU, which is conforming with std::variant, but this could be another improvement over std::variant if it is not too much work to implement. boost::variant is supporting them and I am using this in boost.histogram in this function  
https://github.com/HDembinski/histogram/blob/5ae08d40c26f8db0613545b84a4c0b60c3729102/include/boost/histogram/detail/axes.hpp#L47  
which allows me to get a reference to an element of a std::tuple with a run-time index.  
  
Related SO question:  
https://stackoverflow.com/questions/54218595/why-are-references-forbidden-in-stdvariant

---

## Comment 1

> Username: pdimov  
> Created at: 2019-02-17 14:14:28 UTC  
> Url: https://github.com/boostorg/variant2/issues/5#issuecomment-464462272  

As explained in the SO answer, there's disagreement over assignment. To take your case as an example, if I assign to the return value of `axis_get`, what should happen?

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-02-17 18:06:09 UTC  
> Updated at: 2019-02-17 18:06:44 UTC  
> Url: https://github.com/boostorg/variant2/issues/5#issuecomment-464488980  

I argued in the SO question discussion that I would except a variant which currently holds a reference to act like a reference, so assignment should pass through, not rebind. But I understand from your comments on cpplang slack that you want to keep this open as long as it is open for std::variant, which makes perfect sense.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-06-03 18:02:47 UTC  
> Url: https://github.com/boostorg/variant2/issues/5#issuecomment-498363300  

I think this should be closed. If variant2 is to be a drop-in replacement for std::variant with the exact same interface and behavior, support for references cannot be implemented.
