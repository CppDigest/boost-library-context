# #144 - error: variable 'boost::pfr::detail::fake_object<Test>' is used but not defined [Closed]

> Username: linuxnyasha  
> Created at: 2023-11-20 14:03:41 UTC  
> Updated at: 2023-11-28 16:28:01 UTC  
> Closed at: 2023-11-28 16:27:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/144  

In file included from error.cpp:1:  
In file included from /usr/include/boost/pfr.hpp:14:  
In file included from /usr/include/boost/pfr/core_name.hpp:17:  
In file included from /usr/include/boost/pfr/detail/core_name.hpp:23:  
In file included from /usr/include/boost/pfr/detail/core_name20_static.hpp:21:  
/usr/include/boost/pfr/detail/fake_object.hpp:20:16: error: variable 'boost::pfr::detail::fake_object<Test>' is used but not defined in this translation unit, and cannot be defined in any other translation unit because its type does not have linkage  
   20 | extern const T fake_object;  
      |                ^  
error.cpp:7:29: note: used here  
    7 |   (void)boost::pfr::detail::fake_object<Test>;  
      |                             ^  
  
```cpp  
#include <boost/pfr.hpp>  
  
int main() {  
  struct Test {  
    int data;  
  };  
  (void)boost::pfr::detail::fake_object<Test>;  
};  
```

---

## Comment 1

> Username: schaumb  
> Created at: 2023-11-22 17:27:27 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1823194628  

`fake_object` is intentionally designed to be devoid of linkage, signifying its exclusive purpose for member name retrieval. It serves as a companion to `declval<T>()` by providing a compile-time "memory address" without actual utilization.  
  
This behavior is not a defect, as long as the object remains encapsulated within the detail namespace.   
However, it becomes a bug if the error can be replicated without explicitly using `fake_object`.

---

## Comment 2

> Username: linuxnyasha  
> Created at: 2023-11-23 09:40:57 UTC  
> Updated at: 2023-11-23 09:41:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1824076628  

```cpp  
#include <boost/pfr.hpp>  
int main() {  
  struct Test {  
    int field;  
  };  
  static_assert(boost::pfr::get_name<0, Test>() == "field");  
};  
```

---

## Comment 3

> Username: schaumb  
> Created at: 2023-11-23 10:29:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1824147309  

Could you please provide details about the operating system, compiler (including version), and toolchain you are using? I am facing challenges in replicating the issue, and automated tests are yielding positive results. It's possible that the issue may be specific to your environment.

---

## Comment 4

> Username: linuxnyasha  
> Created at: 2023-11-23 15:22:00 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1824607274  

Gentoo Linux   
```  
clang --version  
clang version 17.0.4  
Target: x86_64-gentoo-linux-musl  
Thread model: posix  
InstalledDir: /usr/lib/llvm/17/bin  
Configuration file: /etc/clang/x86_64-gentoo-linux-musl-clang.cfg  
```

---

## Comment 5

> Username: linuxnyasha  
> Created at: 2023-11-23 15:25:45 UTC  
> Updated at: 2023-11-23 15:29:11 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1824612512  

Can you show me a test that worked successfully? I didn't find one. Error on g++-13 (Gentoo 13.2.1_p20231014 p10) 13.2.1 20231014:  
```  
In file included from /usr/include/boost/pfr/detail/core_name20_static.hpp:21,  
                 from /usr/include/boost/pfr/detail/core_name.hpp:23,  
                 from /usr/include/boost/pfr/core_name.hpp:17,  
                 from /usr/include/boost/pfr.hpp:14,  
                 from lu.cpp:1:  
/usr/include/boost/pfr/detail/fake_object.hpp:20:16: error: ‘const main()::Test boost::pfr::detail::fake_object<main()::Test>’, declared using local type ‘const main()::Test’, is used but never  
defined [-fpermissive]  
   20 | extern const T fake_object;  
```

---

## Comment 6

> Username: schaumb  
> Created at: 2023-11-23 16:22:55 UTC  
> Updated at: 2023-11-23 16:40:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1824688631  

The [limitation](https://www.boost.org/doc/libs/develop/doc/html/boost_pfr/limitations_and_configuration.html) says that   
  
> The Boost.PFRs extraction of field name has some limitations that depend on a C++ Standard and compiler capabilities:  
> T should be usable like extern T t;, i.e. has a non-internal linkage.  
  
Function local classes has no external linkage, so it will not work with those structures.  
  
---  
  
The tests can be found [here](https://github.com/boostorg/pfr/tree/develop/test/core_name), and the tests are run in GitHub actions, like [here](https://github.com/boostorg/pfr/actions/runs/6492490520/job/17631533607#step:5:29250)

---

## Comment 7

> Username: schaumb  
> Created at: 2023-11-23 19:16:07 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1824837009  

Hi @denzor200 ,  
  
Is it possible to rename the `fake_object` or create a pre-checker object with a more meaningful name, such as `passed_type_has_no_external_linkage` with some comment? This would enhance the clarity of error messages.

---

## Comment 8

> Username: denzor200  
> Created at: 2023-11-23 21:54:53 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1824953164  

Hi, @schaumb ,  
  
Sure, PR will be created in the future

---

## Comment 9

> Username: denzor200  
> Created at: 2023-11-25 21:45:45 UTC  
> Url: https://github.com/boostorg/pfr/issues/144#issuecomment-1826426270  

Here is the PR: https://github.com/boostorg/pfr/pull/146
