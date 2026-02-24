# #147 Link bcrypt in CMake tests on MSVC [Closed]

> Username: 13steinj  
> Created at: 2024-04-19 17:11:45 UTC  
> Updated at: 2024-04-30 13:55:01 UTC  
> Closed at: 2024-04-20 22:17:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/147  

See boostorg/uuid#68, boostorg/uuid#78, boostorg/uuid#122, related boostorg/boost_install#54.  
  
---  
  
@pdimov I think you unintentionally broke the cmake tests for 1.85.0 on msvc; given you're involved in the above linked issues ;)  
  
Needed for orgs/individuals that run boost tests in CI, e.g., sanity check to verify that internal / to-be-upstreamed patches haven't broken other behavior.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-19 17:48:50 UTC  
> Url: https://github.com/boostorg/uuid/pull/147#issuecomment-2067034898  

But the Windows CMake tests are passing, see https://github.com/boostorg/uuid/actions/runs/8150026167.

---

## Comment 2

> Username: 13steinj  
> Created_at: 2024-04-19 18:54:59 UTC  
> Url: https://github.com/boostorg/uuid/pull/147#issuecomment-2067122656  

I can't confirm that the following is the cause for at least a few days unfortunately, but I suspect we end up disabling the autolink feature somehow.  
  
I don't know if the cmake tests here should be relying on it, but I would guess not.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-19 19:21:31 UTC  
> Url: https://github.com/boostorg/uuid/pull/147#issuecomment-2067160121  

You are probablu defining `BOOST_ALL_NO_LIB` somewhere, which disables UUID's autolinking to `bcrypt.lib`.  
  
That UUID uses the Boost autolink mechanism to autolink to `bcrypt.lib` is highly questionable in itself, because it's only meant for autolinking to Boost libraries, not to system ones. So I'll probably change that to use `#pragma comment` directly and to not look at `BOOST_ALL_NO_LIB`, although I'm sure this will invite its share of objections from people who do use `BOOST_ALL_NO_LIB` to disable all autolink.  
  
In either case, this isn't quite the right fix. The Boost:uuid target should link to `bcrypt`, and not when the compiler ID is MSVC, but when the platform is Windows.  
  
Although that's not entirely correct either because UUID can use either Bcrypt or Wincrypt and that's determined at compile time.

---

## Comment 4

> Username: 13steinj  
> Created_at: 2024-04-19 20:06:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/147#issuecomment-2067214954  

> In either case, this isn't quite the right fix. The Boost:uuid target should link to bcrypt, and not when the compiler ID is MSVC, but when the platform is Windows.  
  
Happy to make that change. It's been so busy that I didn't notice a top level CMakeLists.txt and target was added in ~1.77  
  
> Although that's not entirely correct either because UUID can use either Bcrypt or Wincrypt and that's determined at compile time.  
  
I was under the impression that wincrypt / MS CryptoAPI was deprecated, and that the alternative (CNG) was introduced with Vista aka 2007 (and possibly, is the same time the CryptoAPI was deprecated). I would suggest that support of use of such an old lib not be maintained; AFAIK even tools on the larger side of things like FFmpeg made the switch years ago.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-04-20 22:17:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/147#issuecomment-2067801774  

I've enabled autolinking to bcrypt.lib even when BOOST_ALL_NO_LIB is defined, which should take care of the issue for now, until a more permanent solution emerges.

---
