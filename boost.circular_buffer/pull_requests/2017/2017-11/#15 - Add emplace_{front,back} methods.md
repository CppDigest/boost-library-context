# #15 Add emplace_{front,back} methods [Open]

> Username: AI0867  
> Created at: 2017-11-12 01:22:28 UTC  
> Updated at: 2023-06-02 20:45:37 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15  



---

## Comment 1

> Username: jmille01  
> Created_at: 2018-09-06 13:32:41 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-419094536  

Is there no maintainer for circular_buffer anymore who can accept this patch?

---

## Comment 2

> Username: glenfe  
> Created_at: 2018-10-02 10:03:26 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-426218877  

There is no active maintainer, but I'm happy to review this (and eventually merge it when it is suitable).

---

## Review 3 [Changes requested]

> Username: glenfe  
> Created_at: 2018-10-02 10:19:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/circular_buffer/pull/15#pullrequestreview-160651610  

Summary of the requested changes:  
* Only define these new function templates when `BOOST_NO_CXX11_VARIADIC_TEMPLATES` is _not_ defined  
* Use `cb_details::allocator_traits` from this library, instead of `boost::container::allocator_traits` from Boost.Container  
* Add unit tests for the new functions

📁 include/boost/circular_buffer/base.hpp

```diff
1450 |     }
1451 | 
1452 |+     template <class ...Args>
```

> Username: glenfe  
> Created_at: 2018-10-02 10:13:22 UTC  
> Updated_at: 2018-10-13 13:40:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#discussion_r221895291  

You shouldn't define these function templates unconditionally, but instead, only when you detect that `BOOST_NO_CXX11_VARIADIC_TEMPLATES` is _not_ defined. This library still supports C++ standards prior to C++11.

---

📁 include/boost/circular_buffer/base.hpp

```diff
1476 |+             } else {
1477 |+                 decrement(m_first);
1478 |+                 boost::container::allocator_traits<Alloc>::construct(m_alloc, cb_details::to_address(m_first), ::boost::forward<Args>(args)...);
```

> Username: glenfe  
> Created_at: 2018-10-02 10:15:15 UTC  
> Updated_at: 2018-10-13 13:40:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#discussion_r221895861  

The library no longer has a dependency on Boost.Container for `boost::container::allocator_traits`. Instead use `cb_details::allocator_traits` which is defined in <boost/circular_buffer/allocators.hpp>.


📁 include/boost/circular_buffer/space_optimized.hpp

```diff
 928 |+     */
 929 |+     template <class ...Args>
 930 |+     void emplace_back(BOOST_FWD_REF(Args) ...args) {
```

> Username: glenfe  
> Created_at: 2018-10-02 10:15:47 UTC  
> Updated_at: 2018-10-13 13:40:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#discussion_r221896023  

Unit tests should be added for these new members in this pull request.


---

## Comment 4

> Username: AI0867  
> Created_at: 2018-10-13 13:43:51 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-429543000  

I believe I've addressed all of your feedback, and rebased onto current develop. Apparently there were some internal interface changes in boost in the meantime.  
I'm not entirely sure about the no-variadic-templates fallback, but it passes the tests I wrote.

---

## Comment 5

> Username: gpascualg  
> Created_at: 2019-06-13 19:02:23 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-501838210  

I am having problems with emplacing when the buffer is full. Trying to emplace a new element when the buffer is full will trigger  
```  
replace(m_first, value_type(::boost::forward<Args>(args)...));  
```  
(https://github.com/boostorg/circular_buffer/pull/15/files#diff-bc2e61cf0e1d5c347de205ddde4a5522R1459)  
Either inside `replace` or during the call, I haven't got the time to fully debug it, the newly created object is destroyed, which triggers it's destructor.  
  
An easy example (out of my mind, not tested):  
```c++  
class Test  
{  
public:  
    Test(int, int) { std::cout << "C " << this << std::nl; }  
    Test(const Test&) = delete;  
    ~Test() { std::cout << "D " << this << std::nl; }  
};  
  
boost::circular_buffer<Test> buffer(2);  
buffer.emplace_back(1, 2);  
buffer.emplace_back(2, 3);  
buffer.emplace_back(3, 4);  
```  
  
I don't know if this is expected behaviour, but in my case it conflicts with some pointers I am moving around.  
  
My quick patch (admittedly not consistent with the rest of the code) is   
```diff  
  
diff --git a/include/boost/circular_buffer/base.hpp b/include/boost/circular_buffer/base.hpp  
index 3502931..9d34c2e 100644  
--- a/include/boost/circular_buffer/base.hpp  
+++ b/include/boost/circular_buffer/base.hpp  
@@ -1456,7 +1456,8 @@ private:  
         if (full()) {  
             if (empty())  
                 return;  
-            replace(m_last, value_type(::boost::forward<Args>(args)...));  
+            cb_details::allocator_traits<Alloc>::destroy(alloc(), boost::to_address(m_last));  
+            cb_details::allocator_traits<Alloc>::construct(alloc(), boost::to_address(m_last), ::boost::forward<Args>(args)...);  
             increment(m_last);  
             m_first = m_last;  
         } else {  
```  
  
AFAIK `emplace_front` should suffer from the same, but I haven't tested it.  
  
PR merged on `boost-1.69` branch and built with `FLAGS = -g  -Wall -Wno-long-long -pedantic -std=gnu++14`  
```  
Using built-in specs.  
COLLECT_GCC=gcc  
COLLECT_LTO_WRAPPER=/usr/lib64/gcc/x86_64-solus-linux/8/lto-wrapper  
Target: x86_64-solus-linux  
Configured with: ../configure --prefix=/usr --with-pkgversion=Solus --libdir=/usr/lib64 --libexecdir=/usr/lib64 --with-system-zlib --enable-shared --enable-threads=posix --enable-gnu-indirect-function --enable-__cxa_atexit --enable-plugin --enable-gold --enable-ld=default --enable-clocale=gnu --enable-multilib --with-multilib-list=m32,m64 --enable-lto --with-gcc-major-version-only --with-bugurl=https://dev.solus-project.com/ --with-arch_32=i686 --enable-linker-build-id --with-linker-hash-style=gnu --with-gnu-ld --build=x86_64-solus-linux --target=x86_64-solus-linux --enable-languages=c,c++,fortran  
Thread model: posix  
gcc version 8.3.1 20190320 (Solus)  
```

---

## Comment 6

> Username: BrukerJWD  
> Created_at: 2021-05-01 06:06:11 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-830559494  

@AI0867, could you please continue this work?

---

## Comment 7

> Username: AI0867  
> Created_at: 2021-05-01 14:56:19 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-830645265  

I hadn't realised someone had replied to this. I've rebased the branch locally, but I don't seem to have a working build environment for boost at the moment, so this may take some time.

---

## Comment 8

> Username: Alexander-Shukaev  
> Created_at: 2021-08-13 22:46:47 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-898756437  

Looking forward to this...

---

## Comment 9

> Username: AI0867  
> Created_at: 2023-02-02 11:45:19 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1413607721  

I've updated the code in line with its surroundings, and fixed the issue where a full buffer creates an intermediate value.  
  
Are there any other changes required?

---

## Comment 10

> Username: lano1106  
> Created_at: 2023-06-01 14:46:16 UTC  
> Updated_at: 2023-06-01 21:38:13 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1572194345  

> I am having problems with emplacing when the buffer is full. Trying to emplace a new element when the buffer is full will trigger  
>   
> ```  
> replace(m_first, value_type(::boost::forward<Args>(args)...));  
> ```  
>   
> (https://github.com/boostorg/circular_buffer/pull/15/files#diff-bc2e61cf0e1d5c347de205ddde4a5522R1459) Either inside `replace` or during the call, I haven't got the time to fully debug it, the newly created object is destroyed, which triggers it's destructor.  
>   
> An easy example (out of my mind, not tested):  
>   
> ```c++  
> class Test  
> {  
> public:  
>     Test(int, int) { std::cout << "C " << this << std::nl; }  
>     Test(const Test&) = delete;  
>     ~Test() { std::cout << "D " << this << std::nl; }  
> };  
>   
> boost::circular_buffer<Test> buffer(2);  
> buffer.emplace_back(1, 2);  
> buffer.emplace_back(2, 3);  
> buffer.emplace_back(3, 4);  
> ```  
>   
> I don't know if this is expected behaviour, but in my case it conflicts with some pointers I am moving around.  
>   
> My quick patch (admittedly not consistent with the rest of the code) is  
>   
> ```diff  
> diff --git a/include/boost/circular_buffer/base.hpp b/include/boost/circular_buffer/base.hpp  
> index 3502931..9d34c2e 100644  
> --- a/include/boost/circular_buffer/base.hpp  
> +++ b/include/boost/circular_buffer/base.hpp  
> @@ -1456,7 +1456,8 @@ private:  
>          if (full()) {  
>              if (empty())  
>                  return;  
> -            replace(m_last, value_type(::boost::forward<Args>(args)...));  
> +            cb_details::allocator_traits<Alloc>::destroy(alloc(), boost::to_address(m_last));  
> +            cb_details::allocator_traits<Alloc>::construct(alloc(), boost::to_address(m_last), ::boost::forward<Args>(args)...);  
>              increment(m_last);  
>              m_first = m_last;  
>          } else {  
> ```  
>   
> AFAIK `emplace_front` should suffer from the same, but I haven't tested it.  
>   
> PR merged on `boost-1.69` branch and built with `FLAGS = -g -Wall -Wno-long-long -pedantic -std=gnu++14`  
>   
> ```  
> Using built-in specs.  
> COLLECT_GCC=gcc  
> COLLECT_LTO_WRAPPER=/usr/lib64/gcc/x86_64-solus-linux/8/lto-wrapper  
> Target: x86_64-solus-linux  
> Configured with: ../configure --prefix=/usr --with-pkgversion=Solus --libdir=/usr/lib64 --libexecdir=/usr/lib64 --with-system-zlib --enable-shared --enable-threads=posix --enable-gnu-indirect-function --enable-__cxa_atexit --enable-plugin --enable-gold --enable-ld=default --enable-clocale=gnu --enable-multilib --with-multilib-list=m32,m64 --enable-lto --with-gcc-major-version-only --with-bugurl=https://dev.solus-project.com/ --with-arch_32=i686 --enable-linker-build-id --with-linker-hash-style=gnu --with-gnu-ld --build=x86_64-solus-linux --target=x86_64-solus-linux --enable-languages=c,c++,fortran  
> Thread model: posix  
> gcc version 8.3.1 20190320 (Solus)  
> ```  
  
I have tried to figure out what the bug was and if there has been any fix related to this...  
  
My conclusion to this is: it is working as designed. In the documentation, it says that if the buffer is full, the last/first element will be destroyed before being replaced.  
  
Keep in mind that the placement new is used. Therefore, the this pointer value will remain the same between the old element being destroyed and the replacing element being constructed.  
  
I'm not too sure what replacing the replace function call by its body at the calling site can change anything.  
  
A more meaningful test would be to store the constructor parameters and print them in the constructor/destructor to discriminate the old and the new object. Using the this pointer for that purpose is inappropriate because of the placement new  
  
IOW: this pointer value when destructor is called will be equal to pos and the this pointer value when the constructor is called will ALSO be equal to pos even if they are 2 distinct objects, they share the same this value because they are located at the same address... Am I missing something?  
  
```  
        boost::allocator_destroy(alloc(), boost::to_address(pos));  
        boost::allocator_construct(alloc(), boost::to_address(pos), ::boost::forward<Args>(args)...);  
```

---

## Comment 11

> Username: gpascualg  
> Created_at: 2023-06-01 22:08:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1572853849  

@lano1106 I don't know if my comments are stil relevant anymore, it's been a few years since I have used `boost::circular_buffer`. Tbh, I don't remeber the use case I needed or why it was relevant.  
  
As far as I remember, the implementation presented here created temporal values during `emplace` when the buffer was full. It is those temporals that were (unexpectly?) constructed and destroyed that conflicted with what I wanted to do.  
I agree that destroying the old value in the buffer before emplacing the new one is required, as shown in the code you quoted.  
AFAIK, #42 does not change this behaviour.  
  
At the time I didn't have time to work on a proper fix, certainly my proposed changes should never be commited as they were, and were meant just as a PoC and quick hack for myself.

---

## Comment 12

> Username: lano1106  
> Created_at: 2023-06-01 22:32:03 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1572876462  

@gpascualg sure. This report is getting old... What I am saying is that because it is a sequence  
  
destructor-constructor, I guess that this can become confusing about which function is called before the other. I have run your test program with the original circular_buffer patch with the perfectly forwarding replace() function:  
  
```  
./a.out   
C 0x564dc9b332b0  
C 0x564dc9b332b1  
D 0x564dc9b332b0  
C 0x564dc9b332b0  
D 0x564dc9b332b1  
D 0x564dc9b332b0  
```  
I am not seeing any temporal getting created and destructed...  
  
Here is a small modification of your test to implement my suggestion  
  
```  
#include <boost/circular_buffer.hpp>  
#include <iostream>  
  
class Test  
{  
public:  
    Test(int a, int b)  
    : m_a(a), m_b(b)  
    { std::cout << "C " << this << " (" << m_a << ',' << m_b << ')' << std::endl; }  
    Test(const Test&) = delete;  
    ~Test() { std::cout << "D " << this << " (" << m_a << ',' << m_b << ')' << std::endl; }  
  
private:  
    int m_a;  
    int m_b;  
};  
  
int main(int argc, char *argv[])  
{  
    boost::circular_buffer<Test> buffer(2);  
    buffer.emplace_back(1, 2);  
    buffer.emplace_back(2, 3);  
    buffer.emplace_back(3, 4);  
  
    return 0;  
}  
```  
the output:  
```  
$ ./a.out   
C 0x564a22b432b0 (1,2)  
C 0x564a22b432b8 (2,3)  
D 0x564a22b432b0 (1,2)  
C 0x564a22b432b0 (3,4)  
D 0x564a22b432b8 (2,3)  
D 0x564a22b432b0 (3,4)  
```  
see no problem with the original patch. imho, the original patch is superior because it respected the original design and style of the class...

---

## Comment 13

> Username: lano1106  
> Created_at: 2023-06-01 22:46:02 UTC  
> Updated_at: 2023-06-01 22:47:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1572885823  

> AFAIK, #42 does not change this behavior.  
  
correct... but I think it is less good because:  
  
1. It bypasses the iterator debugging code found in replace()  
2. It adds code duplication  
3. it departs from the original class design that was respected in the initial patch  
4. does not add any benefit or fix anything AFAIK  
  
I would go a step even further...  
  
The patch may have added unnecessary code duplication with the functions  
emplace_front_impl() and emplace_back_impl()  
  
with compilers supporting variadic templates, I have the feeling that it could be possible to remove   
push_back_impl() and push_front_impl() and replace their calls with emplace_front_impl() and emplace_back_impl()...  
  
but this is just an idea and I would not want the PR to be stopped by this simple improvement idea that may/may not work...

---

## Comment 14

> Username: lano1106  
> Created_at: 2023-06-01 23:18:51 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1572910468  

there is one thing that I wonder about...  
  
How do compilers choose the correct overload replace() when only 1 parameter is passed to the emplace functions?  
  
That might be what the whole bug was about...  
  
I am not a C++ lawyer knowing all the rules about overloading in the presence of variadic template candidates...  
  
maybe just renaming the variadic version of replace to something distinct would fix the bug *and* retain the benefits of the initial proposal...

---

## Comment 15

> Username: gpascualg  
> Created_at: 2023-06-02 17:53:03 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1574105867  

That output is exactly the behaviour I wanted and expected out of this PR.  
But, indeed, it was not the case when I tried it out. It might be, as you mention, that the wrong overload of `replace` was being called.  
I used a fairly old gcc, `gcc version 8.3.1 20190320`. And, probably, also C++14. Maybe it's a non-issue anymore.  
  
As it is, I concur that this diff is better and avoids duplication.

---

## Comment 16

> Username: lano1106  
> Created_at: 2023-06-02 20:45:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/15#issuecomment-1574294594  

no worry. I have been able to reproduce the issue...  
  
I think that the initial PR prior to rework to address the code review was working fine. This is the version that I was using when I did run your test.  
  
The code review rework did introduce the bug with the value_type construct when calling the replace function. I have been able to reproduce it when I started off writing my patch over the last PR version.

---
