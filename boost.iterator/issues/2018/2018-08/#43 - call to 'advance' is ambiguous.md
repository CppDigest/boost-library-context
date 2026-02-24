# #43 - call to 'advance' is ambiguous [Closed]

> Username: wickedmic  
> Created at: 2018-08-31 10:08:42 UTC  
> Updated at: 2018-09-23 17:31:54 UTC  
> Closed at: 2018-09-23 17:08:55 UTC  
> Url: https://github.com/boostorg/iterator/issues/43  

I upgraded my boost library from 1.62 to 1.67 (issue remains with 1.68) and now I'm getting "call to 'advance' is ambiguous". After some investigation it looks like [a using statment in boost/iterator/advance.hpp:80](https://github.com/boostorg/iterator/blob/b2adecb951af025698618f19a3c838bd314966dc/include/boost/iterator/advance.hpp#L80) is causing the issue. The problem arises if `advance` is called unqualified, so that ADL triggers. `boost` 1.65 seems to be the first release with this issue.  
  
In my case this means that I cannot use range-v3 (this lib is making the unqualified call to advance) together with boost geometry (this uses boost-iterator's advance function and therefor includes boost/iterator/advance.hpp).  
  
Here is a minimal code example, which causes this issue:  
https://godbolt.org/z/rSfeVi

---

## Comment 1

> Username: Lastique  
> Created at: 2018-08-31 18:07:36 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-417746589  

I don't think the test case is correct. You can't expect unqualified `advance` to resolve to `std::advance` because the type of the iterator is unspecified (i.e. its type is not required to be in namespace std).  
  
I think the right thing to do is to fix user's code (or range-v3) so that it doesn't call unqualified `advance`.

---

## Comment 2

> Username: ericniebler  
> Created at: 2018-09-19 16:53:11 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-422877357  

> I think the right thing to do is to fix user's code (or range-v3)  
  
I'll note here that the test case has no range-v3 code in it. The right thing to do would be for Boost to not add an overload of `advance` that creates an ambiguity with the one in namespace `std`, and locate it such that ADL is likely to find it. One possible solution would be to turn `boost::advance` into a customization point object.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-09-19 19:13:43 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-422924766  

> The right thing to do would be for Boost to not add an overload of advance that creates an ambiguity with the one in namespace std, and locate it such that ADL is likely to find it.  
  
`advance` in Boost.Iterator is already protected from inadvertent ADL. It's defined in `boost::iterators::advance_adl_barrier`. `boost::advance` and `boost::iterators::advance` are the names for direct public use.

---

## Comment 4

> Username: ericniebler  
> Created at: 2018-09-19 19:28:45 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-422929242  

I don't think you're really understanding the issue. As @wickedmic pointed out above, the version of `advance` in the ADL-protecting namespace is brought into the `boost::` namespace with a using declaration (which rather defeats the purpose of the ADL-protecting namespace) here:  
  
https://github.com/boostorg/iterator/blob/b2adecb951af025698618f19a3c838bd314966dc/include/boost/iterator/advance.hpp#L80  
  
So an unqualified call to `advance` will find _both_ the versions in `boost::` and in `std::` if the iterator type has `boost` and `std` as associated namespaces. That is not an uncommon scenario, I would imagine, especially for users of Boost.Iterator.  
  
The way to make this work is to turn `boost::advance` into a customization point object.

---

## Comment 5

> Username: Lastique  
> Created at: 2018-09-19 20:21:31 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-422944819  

Sorry, I was thinking that ADL does not consider using-declarations, but apparently it only doesn't consider *using-directives*.  
  
Making `boost::advance` a customization point object would solve the ambiguity but I'm not sure making it a *customization point* is what we really want. I don't think we want to dispatch between different implementations of `advance`. Perhaps just converting that using-declaration to a using-directive would suffice.

---

## Comment 6

> Username: morinmorin  
> Created at: 2018-09-22 11:54:49 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423739019  

IIRC, Boost.Iterator has never advertised `boost::advance/distance`.  
The [documentation](https://github.com/boostorg/iterator/blob/develop/doc/advance.rst) only says `boost::iterators::advance/distance`.  
So it might be OK to remove `using iterators::advance/distance;`   
(and submit PR's to users of `boost::advance/distance`).  
But this makes a discrepancy between Boost.Iterator and Boost.Range;   
Boost.Range has `boost::distance`, but Boost.Iterator doesn't have it.  
  
> Perhaps just converting that using-declaration to a using-directive would suffice.  
  
+1 and #44.

---

## Comment 7

> Username: morinmorin  
> Created at: 2018-09-22 13:37:54 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423744799  

Thanks Michael and Eric for reporting and pointing out the problem.  
Also, thanks Andrey for merging the PR (and sorry for forgetting rebase & squash).

---

## Comment 8

> Username: Lastique  
> Created at: 2018-09-22 15:51:54 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423753394  

I had to revert the change as it broke building Boost.

---

## Comment 9

> Username: morinmorin  
> Created at: 2018-09-22 16:54:11 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423757664  

It seems that Boost.Range's `boost::distance(rng)` hides Boost.Iterator's `boost::distance(it1, it2)`, if we use using-directives. So, `boost::distance(it1, it2)` cannot be found when `<boost/range/distance.hpp>` is included.  
  
This sample code fails to compile:  
```  
namespace NS1  
{  
    namespace NS2  
    {  
        template <typename T, typename U>  
        void f(T, U) {}  
    }  
      
    using namespace NS2; // Error  
    // using NS2::f; // OK (but using-declarations have ADL issue)  
      
    template <typename T>  
    void f(T) {}  
}  
  
int main(int argc, char* argv[])  
{  
    NS1::f(1, 2);  
      
    return 0;  
}  
```

---

## Comment 10

> Username: pdimov  
> Created at: 2018-09-22 18:13:04 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423762958  

Iterator's boost::advance should be enable_if-ed on the category being one of Iterator's. (An object won't work - can't overload an object with Range's function.)

---

## Comment 11

> Username: pdimov  
> Created at: 2018-09-22 18:16:33 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423763195  

Looking at https://github.com/boostorg/iterator/blob/b2adecb951af025698618f19a3c838bd314966dc/include/boost/iterator/iterator_categories.hpp#L110, the right condition seems to be `is_convertible<Cat, incrementable_traversal_tag>`.

---

## Comment 12

> Username: pdimov  
> Created at: 2018-09-22 18:18:28 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423763318  

... and it seems that we'll also have to `using std::advance;` in `boost::` to support `boost::advance` for normal iterators.  
  
Tricky.

---

## Comment 13

> Username: morinmorin  
> Created at: 2018-09-22 18:29:17 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423763949  

A possible fix:  
Define Boost.Range's `distance(rng)` in ADL barrier namespace (currently, it is defined in `boost` namespace) and pull the name by a using-directive. Note that [documentation](https://www.boost.org/doc/libs/develop/libs/range/doc/html/range/reference/concept_implementation/semantics/functions.html) of Boost.Range explicitly forbids unqualified call to `distance(rng)`. So this modification to Boost.Range should be OK.  
  
This code compiles fine:  
```  
namespace NS1  
{  
    namespace NS2  
    {  
        template <typename T, typename U>  
        void f(T, U) {}  
    }  
    using namespace NS2;  
      
    namespace NS3  
    {  
        template <typename T>  
        void f(T) {}  
    }  
    using namespace NS3;  
}  
  
int main(int argc, char* argv[])  
{  
    NS1::f(1, 2);  
    NS1::f(1);  
      
    return 0;  
}  
```

---

## Comment 14

> Username: morinmorin  
> Created at: 2018-09-23 16:57:09 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423830740  

Regarding constraints on the first template parameter of `advance`: if ADL issues get resolved, I think "no constraints" would be OK (unless other libraries define `boost::advance` with two unconstrained template parameters).

---

## Comment 15

> Username: pdimov  
> Created at: 2018-09-23 17:07:33 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423831456  

Yes, I agree.

---

## Comment 16

> Username: ericniebler  
> Created at: 2018-09-23 17:31:54 UTC  
> Url: https://github.com/boostorg/iterator/issues/43#issuecomment-423833108  

Nice. Thanks guys.
