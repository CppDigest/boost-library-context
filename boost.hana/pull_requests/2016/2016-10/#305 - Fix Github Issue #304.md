# #305 Fix Github Issue #304 [Closed]

> Username: ricejasonf  
> Created at: 2016-10-22 20:40:26 UTC  
> Updated at: 2016-10-23 20:30:44 UTC  
> Closed at: 2016-10-23 20:30:44 UTC  
> Url: https://github.com/boostorg/hana/pull/305  

- Apparently std::array operator[] returns an lvalue reference when unpacking  
  a temporary std::array. Using std::get<n> returns the  
  proper reference type in the case of rvalues.

---

## Review 1 [Commented]

> Username: ldionne  
> Created_at: 2016-10-23 18:01:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/305#pullrequestreview-5368796  

Thanks for this! I also had started writing a patch to fix this, but you were faster. Can you please add the following test case in `test/ext/std/array/at.cpp`:  
  
``` c++  
// Copyright Louis Dionne 2013-2016  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)  
  
#include <boost/hana/ext/std/array.hpp>  
  
#include <boost/hana/assert.hpp>  
#include <boost/hana/at.hpp>  
  
#include <laws/base.hpp> // for move_only  
  
#include <array>  
namespace hana = boost::hana;  
  
  
int main() {  
    // Check non-const lvalue reference  
    {  
        std::array<int, 2> arr = {{999, 888}};  
        int& i = hana::at_c<0>(arr);  
        BOOST_HANA_RUNTIME_CHECK(i == 999);  
        arr[0] = 333;  
        BOOST_HANA_RUNTIME_CHECK(i == 333);  
        i = 444;  
        BOOST_HANA_RUNTIME_CHECK(arr[0] == 444);  
    }  
  
    // Check const lvalue reference  
    {  
        std::array<int, 2> arr = {{999, 888}};  
        int const& i = hana::at_c<0>(static_cast<std::array<int, 2> const&>(arr));  
        BOOST_HANA_RUNTIME_CHECK(i == 999);  
        arr[0] = 333;  
        BOOST_HANA_RUNTIME_CHECK(i == 333);  
    }  
  
    // Check move-only types  
    {  
        std::array<hana::test::move_only, 2> arr{};  
        hana::test::move_only m = hana::at_c<0>(std::move(arr));  
        (void)m;  
    }  
}  
```

📁 test/issues/github_304.cpp

```diff
   1 |+ // Copyright Jason Rice 2016
```

> Username: ldionne  
> Created_at: 2016-10-23 17:50:00 UTC  
> Updated_at: 2016-10-23 18:26:35 UTC  
> Url: https://github.com/boostorg/hana/pull/305#discussion_r84600459  

Can we please rename this file  to `test/ext/std/array/issue_304.cpp`? This way, the dependency upon external adapters is explicit.


---

## Comment 2

> Username: ricejasonf  
> Created_at: 2016-10-23 18:29:11 UTC  
> Url: https://github.com/boostorg/hana/pull/305#issuecomment-255605185  

I added your file and renamed the other test. (I could remove it if you think it is redundant.)  
  
Sorry about referencing the issue in the commit message. I forgot that it did that. :sweat_smile:

---

## Comment 3

> Username: ldionne  
> Created_at: 2016-10-23 18:30:40 UTC  
> Url: https://github.com/boostorg/hana/pull/305#issuecomment-255605284  

Thanks a lot! I'll merge this as soon as the CIs are done.

---

## Comment 4

> Username: ldionne  
> Created_at: 2016-10-23 20:30:44 UTC  
> Url: https://github.com/boostorg/hana/pull/305#issuecomment-255612310  

IDK why this was not closed automatically when I merged, but anyway it's merged as ea571a11fc5d3dc6fd2f73a0b1e7e9c0be2fc614.

---
