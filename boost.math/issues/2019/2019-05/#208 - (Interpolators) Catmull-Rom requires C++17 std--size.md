# #208 - (Interpolators) Catmull-Rom requires C++17 std::size [Closed]

> Username: hazelnusse  
> Created at: 2019-05-21 18:51:18 UTC  
> Updated at: 2019-05-30 20:28:03 UTC  
> Closed at: 2019-05-30 20:28:03 UTC  
> Url: https://github.com/boostorg/math/issues/208  

There are several uses of `std::size` in `include/boost/math/interpolators/catmull_rom.hpp`. This is only available in C++17 and prevents the use of this file in projects using older language standards. Would it be appropriate to use .size() or boost::size() be used instead?  See:  
https://www.boost.org/doc/libs/1_70_0/libs/range/doc/html/range/concepts/random_access_range.html  
  
.size() has the disadvantage of not working for C arrays, while boost::size() introduces a dependency on another part of boost that may not be desirable.

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-05-21 21:14:12 UTC  
> Url: https://github.com/boostorg/math/issues/208#issuecomment-494561162  

@hazelnusse : Man, you're rubbing salt in my wounds here!  
  
I don't feel like I did a great job making this function generically useful. I had to constrain people in a number of ways that I really didn't like.  First of all, there seems to be zero consensus about how a geometric point should be represented. Is it `Point p; p.x`, or `get<x>(p)`, or `p[0]`? I opted for   
array syntax, but maybe there's some template magic I could've used to make this more useful.    
  
Then the `std::size`, omg.  `std::size` looks like it's a train wreck; see [here](https://quuxplusone.github.io/blog/2018/06/17/std-size/). And I already submitted a bug report to Armadillo over this one, which caused those guys to really rend their garments.  
  
But unfortunately, I don't see any good ways around it. First off, if we use `boost::size`, we'll get slapped on the wrist for making boost more of a tangled web than it already is. If I get rid of the `std::size`, and convert to the `.size()` syntax, then everyone who followed the docs to create a static `size()` free function will get their code broken, and as you recognize the C arrays break.  
  
So I'm not sure there's anything I can do about it. One thing I note: There are no dependencies in `catmull_rom.hpp` other than standard library headers. So you could vendorize the file and remove the `using std::size` and use ADL to find your Points implementation of `size(p)`.  
  
Not ideal, but will it work for you?

---

## Comment 2

> Username: hazelnusse  
> Created at: 2019-05-21 23:41:04 UTC  
> Updated at: 2019-05-21 23:41:54 UTC  
> Url: https://github.com/boostorg/math/issues/208#issuecomment-494598569  

@NAThompson I wholly concur it's not a pretty situation. However, on my current machine (Ubuntu 16.04), the `using std::size` lines:  
https://github.com/boostorg/math/blob/develop/include/boost/math/interpolators/catmull_rom.hpp#L25  
https://github.com/boostorg/math/blob/develop/include/boost/math/interpolators/catmull_rom.hpp#L128  
https://github.com/boostorg/math/blob/develop/include/boost/math/interpolators/catmull_rom.hpp#L188  
  
prevent compilation of this program, even built with `-std=c++17` and g++5.4:  
```  
#include <boost/math/interpolators/catmull_rom.hpp>  
int main() {}  
```  
Specifically, the errors are `error: ‘std::size’ has not been declared` on the lines I mentioned above. So we are DOA here.  
  
This is partially my fault for using such an old platform but this is at work so there is a long complicated list of reasons why I can't change that immediately. The documentation suggests a free `size()` function at global scope, but I think really it must be in the `std` namespace, right? The following works under my environment:  
  
```  
#include <array>  
#include <vector>  
  
namespace std {  
template <class T, std::size_t N>  
constexpr std::size_t size(const std::array<T, N>& c) {  
  return c.size();  
}  
  
template <class T, class A>  
constexpr std::size_t size(const std::vector<T, A>& c) {  
  return c.size();  
}  
  
}  // namespace std  
#include <boost/math/interpolators/catmull_rom.hpp>  
int main() {}  
```  
So, in my particular situation, the options I can see are:  
  
- define `std::size` as above  
- upgrade my toolchain  
- vend my own version of boost and patch the offending lines, probably by using `#if defined(__cplusplus) && (__cplusplus >= 201703L) ... #endif`  
  
I've made this last change here -- let me know what you think:  
https://github.com/hazelnusse/math/tree/conditional_size

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-05-22 00:21:06 UTC  
> Updated at: 2019-05-22 01:38:56 UTC  
> Url: https://github.com/boostorg/math/issues/208#issuecomment-494605934  

@hazelnusse : I like your solution here. It does mean that people on pre-C++17 might break using a `std::array` as the point type, but the alternative is that it won't work at all.  
  
@jzmaddock : Merge if the builds pass?

---

## Comment 4

> Username: hazelnusse  
> Created at: 2019-05-22 04:28:08 UTC  
> Updated at: 2019-05-22 04:28:39 UTC  
> Url: https://github.com/boostorg/math/issues/208#issuecomment-494647552  

@NAThompson pre-C++17 doesn't have std::size as part of the standard so they should fail even without my change, right? I don't understand why/how `develop` has passed travis-ci builds that use `-std=c++03`, `-std=c++11` or `-std=c++14`. I suspect that it indicates that the standard library headers found in the build are not conditionally defining std::size based on the value of __cplusplus and it just so happens that the toolchains you have configured with CI all have `std::size` defined and available, even for pre c++17 builds. That is my best guess at least.

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-05-22 13:49:13 UTC  
> Updated at: 2019-05-22 15:53:51 UTC  
> Url: https://github.com/boostorg/math/issues/208#issuecomment-494810638  

@hazelnusse : Yeah, they should fail without my change, but the failure is more intelligible when the documentation says "requires C++17". (Probably nobody reads the docs anyway, unless they get a failure.)  
  
The reason why the build is successful is because `test/Jamfile.v2` determines which tests get run under which language standards.  
  
More puzzling is why AppVeyor didn't run . . .

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-05-22 18:43:12 UTC  
> Url: https://github.com/boostorg/math/issues/208#issuecomment-494917900  

Please see https://github.com/boostorg/math/pull/210 which back ports the header to C++11 (maybe even 03, but I couldn't be bothered to modify the test program to discover that), the changes were pretty trivial too...  lets see how that goes.

---

## Comment 7

> Username: NAThompson  
> Created at: 2019-05-30 20:28:03 UTC  
> Url: https://github.com/boostorg/math/issues/208#issuecomment-497474120  

Fixed in #210.
