# #31 - Warning C4834 occurs in the line 355 of concept_check.hpp [Closed]

> Username: Cheney-W  
> Created at: 2021-01-27 02:13:21 UTC  
> Updated at: 2021-05-07 02:33:28 UTC  
> Closed at: 2021-05-07 02:33:28 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31  

I'm a member of Mrcrosoft VCPKG team, when we test vcpkg with an internal version of VS, pagmo2 installation failed with below error:  
D:\installed\x64-windows\include\boost/concept_check.hpp(355): error C2220: the following warning is treated as an error  
D:\installed\x64-windows\include\boost/concept_check.hpp(355): warning C4834: discarding return value of function with 'nodiscard' attribute  
  
This warning caused by the change: https://github.com/microsoft/STL/pull/1474 , [nodiscard] was added before some operator() function and the change of STL is by design.  
  
Since the warning was reported in the concept_check.hpp file, I think we can make some modifications on boost-concept-check to fix this issue, such as comment the line 355. I'm not sure if this will change the function function, please help to confirm.  
Thank you!

---

## Comment 1

> Username: mclow  
> Created at: 2021-01-27 02:26:43 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-767968624  

What if we just changed line #355 to `(void) f(first,second);` or (always my favorite) `std::ignore = f(first, second)`.  
I'm not really serious about `std::ignore`, but I like that you can assign anything to it, and it does nothing.

---

## Comment 2

> Username: Cheney-W  
> Created at: 2021-01-27 03:11:33 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-767985914  

@mclow   
Thanks for your comment. I have tested the two modifications you provided and they can all solve the warning C4834.   
But as @StarGate-One said, there are 20 downstream ports that depend on the function of concept_check, so could you please help confirm that any of the above two modifications did not change the functionality of library concept_check? Thanks in advance!

---

## Comment 3

> Username: marstaik  
> Created at: 2021-03-04 00:08:06 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-790175292  

Recently also ran into this issue due to the visual studio 16.9.0 updated.  
`std::equal_to` was tagged with a `[[nodiscard]]` attribute in the latest stl update.  
  
Confirmed working fix by just casting #355 to `(void)`.

---

## Comment 4

> Username: ghost  
> Created at: 2021-03-08 17:29:16 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-792928426  

I would recommend a solution like it was done to ignore unused variable warnings in `concept_checks.hpp`.  
  
1. Create function below `ignore_unused_variable_warning` line 53 : `template<class T> inline void ignore_nodiscard_warning(T const&){}`  
2. Replace `f(arg);` in line 326 with `ignore_nodiscard_warning(f(arg));`  
3. Replace `f(first, second);` in line 355 with `ignore_nodiscard_warning(f(first, second));`  
  
Remark: I am new in github and still learning the possibilities. I am currently not aware how to create a pull-request with this fix. So I described the changes in the commend.

---

## Comment 5

> Username: mloskot  
> Created at: 2021-03-09 13:02:32 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-793869621  

I've just asked on the ML [[concept_check] Fix issue #31 for Boost 1.76?](https://lists.boost.org/Archives/boost/2021/03/251035.php)

---

## Comment 6

> Username: mloskot  
> Created at: 2021-03-09 15:47:33 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-794071167  

@mclow I confirm your https://github.com/boostorg/concept_check/commit/85fd1e434f3684ec2358af556ccf73d399dfa1fd fixes the issue. Thanks!  
  
For records, here is a sample to reproduce the issue, https://godbolt.org/z/3Tx341  
  
```cpp  
#include <boost/concept_check.hpp>  
#include <functional>  
int main()  
{  
    boost::BinaryFunctionConcept<std::less<int>, bool, int, int>();  
}  
```  
  
```  
```

---

## Comment 7

> Username: gjasny  
> Created at: 2021-04-06 22:56:13 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-814486466  

@mclow This fix is not on master, yet. Is it too late for 1.76?

---

## Comment 8

> Username: mloskot  
> Created at: 2021-05-06 14:16:48 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-833559578  

@gjasny Boost 1.76 does ship the https://github.com/boostorg/concept_check/commit/85fd1e434f3684ec2358af556ccf73d399dfa1fd fix  
https://github.com/boostorg/concept_check/blob/boost-1.76.0/include/boost/concept_check.hpp#L355  
  
@mclow I believe this can be closed now.

---

## Comment 9

> Username: Cheney-W  
> Created at: 2021-05-07 02:33:28 UTC  
> Url: https://github.com/boostorg/concept_check/issues/31#issuecomment-834014924  

Thank you for your reply and help.
