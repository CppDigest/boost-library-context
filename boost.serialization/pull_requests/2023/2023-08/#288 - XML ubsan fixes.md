# #288 XML ubsan fixes [Merged]

> Username: cmazakas  
> Created at: 2023-08-22 19:48:30 UTC  
> Updated at: 2023-08-24 17:59:28 UTC  
> Merged at: 2023-08-23 18:58:33 UTC  
> Closed at: 2023-08-23 18:58:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/288  

The XML oarchive class generates a few ubsan failures, this PR aims to fix them.  
  
To replicate the failures on develop:  
```bash  
b2 -q test//test_mult_archive_types cxxstd=20 undefined-sanitizer=norecover address-sanitizer=norecover  link=static toolset=clang-16  
```  
  
The first failure is that we're incrementing a nullptr in the `increment()` of the `xml_escape` iterator because we never assigned `m_bnext` to non-null.  
  
The second is a static_cast to a Base that we just replace with a reinterpret_cast, trying to utilize the common initial sequence to silence the sanitizer.  
  
We found this in Unordered when adding Serialization support to some of our containers.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-08-24 08:02:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/288#issuecomment-1691203045  

This change is broken and causes crashes.

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-08-24 08:03:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/288#issuecomment-1691203523  

```  
.\boost/archive/detail/interface_oarchive.hpp(55,16): warning: 'reinterpret_cast' to class 'boost::archive::text_oarchive *' from its base at non-zero offset 'boost::archive::detail::interface_oarchive<boost::archive::text_oarchive> *' behaves differently from 'static_cast' [-Wreinterpret-base-class]  
        return reinterpret_cast<Archive*>(this);  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
.\boost/archive/detail/interface_oarchive.hpp(78,15): note: in instantiation of member function 'boost::archive::detail::interface_oarchive<boost::archive::text_oarchive>::This' requested here  
        this->This()->save_override(t);  
              ^  
```

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-08-24 08:03:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/288#issuecomment-1691203802  

```  
testing.capture-output bin.v2\libs\msm\test\SerializeSimpleEuml.test\msvc-14.2\release\cxxstd-latest-iso\threading-multi\SerializeSimpleEuml.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "my_test": memory access violation occurred at address 0x4, while attempting to  read inaccessible data  
libs\msm\test\SerializeSimpleEuml.cpp(134): last checkpoint  
  
*** 1 failure is detected in the test module "MyTest"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
```

---

## Comment 4

> Username: robertramey  
> Created_at: 2023-08-24 17:45:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/288#issuecomment-1692154958  

I was sort of skeptical of this change.  But I didn't have the time/inclination to really investigate it so I merged it.  On my local machine All tests still passed.  I'll change this back to the way it was.

---

## Comment 5

> Username: robertramey  
> Created_at: 2023-08-24 17:45:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/288#issuecomment-1692155177  

Done!

---

## Comment 6

> Username: cmazakas  
> Created_at: 2023-08-24 17:59:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/288#issuecomment-1692173467  

Yeah, I'd like to apologize for not vetting my changes more carefully.  
  
Initially, I was overwhelmed by the size of Serialization and how it was laid out.  
  
Going forward in the future, I'll test changes locally a bit more carefully before opening another PR.

---
