# #64 Add class version [Closed]

> Username: HDembinski  
> Created at: 2019-10-24 22:17:40 UTC  
> Updated at: 2020-02-03 16:47:45 UTC  
> Closed at: 2020-02-03 16:47:45 UTC  
> Url: https://github.com/boostorg/core/pull/64  

nvp was added to core, which is great to write serialize functions that do not depend directly on boost::serialization. but we also need boost::serialization::version in core, which is needed to inform boost serialization that the serialization format has changed.  
  
The following patch has a simple implementation and some unit tests, but no documentation. There is some issue with the name. I followed the example of nvp and pulled serialization::version into the boost namespace, which yields boost::version. I would prefer if it was called boost::class_version, but I don't know how to make a templated alias without C++11 support.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-10-24 22:26:29 UTC  
> Url: https://github.com/boostorg/core/pull/64#issuecomment-546127728  

Your patch doesn't make sense as written. It defines `boost::serialization::version` but tests `boost::version`.  
  
What we could do is define `boost::class_version` and then derive `boost::serialization::version` from it for compatibility, although this could turn out a bit problematic if people start specializing both.  
  
I'd advise against `int_c`. Just define `value` directly inside `version`, and drop `value_type` and the operator.

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-10-25 06:15:25 UTC  
> Url: https://github.com/boostorg/core/pull/64#issuecomment-546217276  

I don't like that we're adding bits of Boost.Serialization to Boost.Core. I'd rather we have Boost.SerializationCore for that, much like Boost.ThrowException.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2019-10-25 08:31:09 UTC  
> Updated_at: 2019-10-25 08:31:23 UTC  
> Url: https://github.com/boostorg/core/pull/64#issuecomment-546258123  

@pdimov You are right, it was late yesterday and I messed up the version that I pushed.  
  
I used the approach you suggested to emulate an alias template, which is actually even better than a alias template now that I think about it. People who manually specialized boost::serialization::version will be ok, since boost.serialization will still use boost::serialization::version internally and it does not matter which version you specialize, assuming nobody will specialize both for the same type, which would be crazy.  
  
I made `int_c` a separate class, because you sometimes want to not use the BOOST_CLASS_VERSION macro. When you want to version a templated class, you cannot use the macro. You have to manually write  
```  
template <class T>  
struct my_class {};  
  
// specializing class_version for all my_class  
namespace boost {  
template <class T>  
struct class_version<my_class<T>> : ::boost::core::int_c<1> {};  
}  
```  
This seems to leak less implementation details than  
```  
template <class T>  
struct my_class {};  
  
// specializing class_version for all my_class  
namespace boost {  
template <class T>  
struct class_version<my_class<T>> {  
   enum { value = 1 };  
};  
}  
```

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-02-03 16:47:45 UTC  
> Url: https://github.com/boostorg/core/pull/64#issuecomment-581507622  

I explained to Hans why we don't want to do this (and shouldn't be making any pull requests without agreement from Robert, which we don't have for this anyway).

---
