# #273 - [1.85] flat_map/vector crashes on appends (memory corruption) [Closed]

> Username: Lastique  
> Created at: 2024-04-19 20:29:42 UTC  
> Updated at: 2024-08-01 21:40:49 UTC  
> Closed at: 2024-05-23 21:53:42 UTC  
> Url: https://github.com/boostorg/container/issues/273  

Consider this test code:  
  
```  
#include <cstdint>  
#include <string>  
#include <boost/container/flat_map.hpp>  
  
typedef boost::container::flat_map< std::uint8_t, std::string > my_map;  
  
void add_element(std::string& str, char elem)  
{  
    str.push_back(elem);  
}  
  
int main()  
{  
    my_map m;  
    add_element(m[static_cast< std::uint8_t >(96u)], 'a');  
    add_element(m[static_cast< std::uint8_t >(102u)], 'a');  
    add_element(m[static_cast< std::uint8_t >(104u)], 'a');  
}  
```  
  
```  
g++ -O2 -I. -std=gnu++17 -o test_flat_map test_flat_map.cpp  
```  
  
This crashes with:  
  
```  
free(): invalid pointer  
Aborted (core dumped)  
```  
  
`valgrind` also shows a number of invalid memory accesses, see the attached log:  
  
[test_flat_map.log](https://github.com/boostorg/container/files/15045401/test_flat_map.log)  
  
This code works correctly in Boost 1.84.0. It also doesn't crash if compiled with `-O0`.  
  
gcc 11.4.0, Kubuntu 22.04.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-04-19 20:39:19 UTC  
> Updated at: 2024-04-19 21:12:30 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2067255097  

Bisect shows that the first bad commit is 1a4a205ea6ef7b4e67a2faab7c7d745711807695. Reverting this commit on 1.85 (with resolved conflicts) fixes the crash.  
  
[container_revert_inline_conversion.patch.gz](https://github.com/boostorg/container/files/15045677/container_revert_inline_conversion.patch.gz)

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-04-28 21:32:58 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2081663265  

Thanks for the report!  
  
I think the issue is produced because flat_map used UB in the implementation, long ago, when C++03 compilers had no movable std::pair type and the class was designed to achieve move emulation in those compilers. The following commit should fix the issue,  your example seems to work after the commit, but I didn't want to close the issue without having your feedback:  
  
https://github.com/boostorg/container/commit/20ad12f20e661978e90dc7f36d8ab8ac05e5a5a9

---

## Comment 3

> Username: Lastique  
> Created at: 2024-04-29 13:07:55 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2082695974  

Thanks, the commit does fix the problem.  
  
It is probably worth attaching this patch to the 1.85.0 [release notes](https://www.boost.org/users/history/version_1_85_0.html).

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-05-23 21:53:42 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2128079149  

Closing this issue as fixed.

---

## Comment 5

> Username: haferburg  
> Created at: 2024-07-09 07:44:20 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2216844143  

@igaztanaga How come this was not caught by a unit test?

---

## Comment 6

> Username: igaztanaga  
> Created at: 2024-07-10 08:55:01 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2219940672  

I think it was optimization-level and context code (inlining decisions) dependent and Boost.Container tests only failed consistently with latest GCC versions (>=11, I think). MSVC and clang showed no failure.  
  
Once latest GCC versions were mainstream, boost.Container tests were failing and I figure out that UB could be the cause.

---

## Comment 7

> Username: 13steinj  
> Created at: 2024-07-19 20:54:47 UTC  
> Updated at: 2024-07-23 03:35:04 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2240090119  

FYI Similar bug (UB => `-O2` code elision) occurs in this example, ~~still in process of checking if the mentioned commit fixes the problem~~ the mentioned commit fixes this (near-)minimal reproducible example: https://gcc.godbolt.org/z/qM5befxeY  
  
```cpp  
#include <boost/container/flat_map.hpp>  
  
struct A {  
    int i;  
    auto operator<=>(const A&) const = default;  
};  
  
int main() {  
    boost::container::flat_map<A, A> map;  
    map.emplace(A{1}, A{});  
    for (auto const& [_1, _2] : map)  
        throw 123; // this line is never executed, UB!  
}  
```  
  
Might be worth getting a patch posted on https://www.boost.org/patches/ and https://www.boost.org/users/history/version_1_85_0.html as a known issue (e.g. how it was done for the 1.83.0 and earlier releases).   
  
E: So, fun. This wasn't caught internally to my org because  
  
* We use the cmake build, as-part-of-source-tree.  
* The cmake build would never end up building any tests: the [referenced file](https://github.com/boostorg/container/blob/4ea9c64316766590e297960f8b26bc04ac0af4d8/CMakeLists.txt#L43) is just [non-existent](https://github.com/boostorg/container/blob/4ea9c64316766590e297960f8b26bc04ac0af4d8/test/CMakeLists.txt).  
* `-DNDEBUG` also hides the issue (at least [the one that I'm referring to](https://gcc.godbolt.org/z/a9x94Wj5d), and the [original one reported](https://gcc.godbolt.org/z/6qd8Exh6z)) from occurring / "elides" the UB.  
* Even if all that was not at issue, the current Jamfile's format [does not appear to be supported by Dimov's `boost_test_jamfile` converter / translator](https://github.com/boostorg/cmake/blob/ae2e6a647187246d6009f80b56ba4c2c8f3a008c/include/BoostTestJamfile.cmake#L53-L57) since it doesn't explicitly specify the tests.

---

## Comment 8

> Username: justusranvier  
> Created at: 2024-07-26 18:02:07 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2253233204  

Does boost-1.86.0-beta1 include the fix for this?

---

## Comment 9

> Username: igaztanaga  
> Created at: 2024-07-26 22:34:21 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2253596493  

Yes. Release notes were not updated for this beta but the fix is there.

---

## Comment 10

> Username: igaztanaga  
> Created at: 2024-07-27 22:27:47 UTC  
> Updated at: 2024-07-27 22:28:18 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2254270167  

Trying to build a patch for 1.85 I could not reproduce the bug with Boost.Build. It turns out that Build defines -DNDEBUG and that avoids @Lastique's example's crash...  
  
Anyway, here's the first patch for 1.85 if you want to test it:  
  
[0001-container-fix-flat_map.patch](https://github.com/user-attachments/files/16401624/0001-container-fix-flat_map.patch)

---

## Comment 11

> Username: Lastique  
> Created at: 2024-07-30 09:37:03 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2257917340  

The patch is better placed on the website and linked in 1.85 release notes. See an example of a PR doing this [here](https://github.com/boostorg/website/pull/751).

---

## Comment 12

> Username: igaztanaga  
> Created at: 2024-08-01 21:40:48 UTC  
> Url: https://github.com/boostorg/container/issues/273#issuecomment-2264059644  

Many thanks for your example Andrey. PR:  
  
https://github.com/boostorg/website/pull/862
