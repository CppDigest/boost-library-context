# #166 - boostorg/uuid@0f84313 - broke `boost::compute` [Closed]

> Username: nega0  
> Created at: 2024-08-22 02:25:07 UTC  
> Updated at: 2025-09-01 02:02:35 UTC  
> Closed at: 2025-09-01 02:02:35 UTC  
> Url: https://github.com/boostorg/uuid/issues/166  

```console  
$ brew info boost  
==> boost: stable 1.86.0 (bottled), HEAD  
Collection of portable C++ source libraries  
https://www.boost.org/  
Installed  
/opt/homebrew/Cellar/boost/1.86.0 (16,215 files, 518.0MB) *  
  Poured from bottle using the formulae.brew.sh API on 2024-08-21 at 15:08:42  
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/b/boost.rb  
License: BSL-1.0  
==> Dependencies  
Required: icu4c ✔, xz ✔, zstd ✔  
==> Options  
--HEAD  
	Install HEAD version  
  
$ command cat main.cc  
#include <boost/compute.hpp>  
  
int main(){}  
  
$ gcc -o main -I/opt/homebrew/include -std=c++11 -Wno-deprecated main.cc  
In file included from main.cc:1:  
In file included from /opt/homebrew/include/boost/compute.hpp:14:  
In file included from /opt/homebrew/include/boost/compute/algorithm.hpp:18:  
In file included from /opt/homebrew/include/boost/compute/algorithm/accumulate.hpp:20:  
In file included from /opt/homebrew/include/boost/compute/algorithm/reduce.hpp:20:  
In file included from /opt/homebrew/include/boost/compute/detail/meta_kernel.hpp:39:  
/opt/homebrew/include/boost/compute/detail/sha1.hpp:41:26: error: non-const lvalue reference to type 'digest_type' (aka 'unsigned char[20]') cannot bind to a value of unrelated type 'unsigned int[5]'  
            h.get_digest(digest);  
                         ^~~~~~  
/opt/homebrew/include/boost/uuid/detail/sha1.hpp:179:43: note: passing argument to parameter 'digest' here  
inline void sha1::get_digest(digest_type& digest)  
                                          ^  
1 error generated.  
  
$  
```  
There are two issues here.  
1. 0f84313 broke boostorg/compute  
2. This breaking change made it through the boost release process.   
  
@pdimov given that 0f84313 is your change, and you were also the last to commit to `boost::compute` @ [36350b7](https://github.com/boostorg/compute/commit/36350b7de849300bd3d72a05d8bf890ca405a014) maybe you can get us a 1.86.1 sooner rather than later.

---

## Comment 1

> Username: nega0  
> Created at: 2024-08-22 02:29:40 UTC  
> Updated at: 2024-08-22 02:30:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/166#issuecomment-2303557361  

See https://github.com/boostorg/compute/issues/889  
See https://github.com/boostorg/compute/pull/888  
See https://github.com/boostorg/compute/pull/887

---

## Comment 2

> Username: pdimov  
> Created at: 2024-08-22 07:30:15 UTC  
> Url: https://github.com/boostorg/uuid/issues/166#issuecomment-2303975221  

My mistake; I didn't notice that Boost.Compute was using an implementation detail of UUID.

---

## Comment 3

> Username: nega0  
> Created at: 2024-08-22 15:29:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/166#issuecomment-2305027279  

Thank you for following up on the Boost ML, it seems my email there is "stuck". Probably for the best, anyway. I don't think anyone would expect you or any boost maintainer to notice an integration issue like this on every commit or merge. As a Boost consumer I do expect an issue like this to be caught in the lead up to release. Hopefully this issue can lead to a broader discussion of the "release manager's" role and responsibilities.

---

## Comment 4

> Username: jeking3  
> Created at: 2024-09-07 14:33:51 UTC  
> Url: https://github.com/boostorg/uuid/issues/166#issuecomment-2335271106  

> Boost.Compute was using an implementation detail of UUID  
  
This underscores the need for more automation to find and remove invalid use of implementation details across packages.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-09-07 14:40:45 UTC  
> Url: https://github.com/boostorg/uuid/issues/166#issuecomment-2335294285  

Compute's tests require OpenCL and are automatically disabled when it's not available, which is why all the automation in the world missed it.

---

## Comment 6

> Username: jeking3  
> Created at: 2024-09-07 14:42:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/166#issuecomment-2335302106  

Agree that not having CI on Boost.Compute is also a problem.  However, -if- we had a code scanner to detect this inappropriate use of implementation details, and folks did not violate the rules, it never would have happened.
