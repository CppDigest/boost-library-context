# #372 add eytzinger to benchmarks [Closed]

> Username: HDembinski  
> Created at: 2022-12-22 12:37:06 UTC  
> Updated at: 2022-12-24 11:52:17 UTC  
> Closed at: 2022-12-24 11:52:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/372  

Replace standard search in axis::variable with faster eytzinger search.  
  
Closes #369   
  
@jhcarl0814 Please fork this branch to work on this feature. I made a start and added the benchmark.  
  
#### To-do  
- [x] HD: fix the bugs so the tests run again (this has to happen first)  
- [ ] Implement eytzinger_search as an implementation detail (`histogram/detail/eytzinger_search.hpp`)  
- [ ] Replace standard search in `axis/variable.hpp` with new search, calling implementation in `detail/eytzinger_search.hpp`  
- [ ] Check that implementation works as expected and is faster (existing tests should all pass, perhaps we need further tests)

---

## Comment 1

> Username: jhcarl0814  
> Created_at: 2022-12-23 03:01:49 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1363564985  

@HDembinski I was trying to reproduce the errors caused by clang version changing from 10 to 14 (seems the commit disappered at this noon so do the errors). But following `boost/libs/histogram/CONTRIBUTING.md`, running tests and examples gives no error, running coverage (the `tools/cov.sh` does not exist so I ran `python3 tools/cov.py`) gives an empty `coverage-report` directory.  
If I can not reproduce those errors, something must be wrong in my commands:  
```  
on Windows 11 C:/, shift + right_click >> Open Linux Shell Here  
(CONTRIBUTING.md https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview :)  
git clone --recursive https://ghp_...@github.com/boostorg/boost.git  
cd boost  
git checkout develop  
./bootstrap.sh  
./b2 headers  
  
cd libs/histogram/  
git remote add forking_repo https://ghp_...@github.com/jhcarl0814/histogram.git  
git fetch forking_repo  
git checkout -b eytzinger_search forking_repo/eytzinger_search  
  
../../b2 cxxstd=latest warnings-as-errors=on test -j4  
../../b2 cxxstd=latest examples -j4  
  
sudo apt install python3-pip  
pip install b2filt  
PATH="$PATH:/home/eajniah/.local/bin"  
b2filt cxxstd=latest warnings-as-errors=on test -j4  
b2filt cxxstd=latest examples -j4  
  
(https://stackoverflow.com/questions/62215963/how-to-install-gcc-and-gdb-for-wslwindows-subsytem-for-linux :)  
sudo apt-get update && sudo apt-get upgrade -y  
sudo apt-get install gcc-8 -y  
sudo apt-get install g++-8 -y  
../../b2 toolset=gcc-8 cxxstd=latest coverage=on test  
```

---

## Comment 2

> Username: HDembinski  
> Created_at: 2022-12-23 08:11:30 UTC  
> Updated_at: 2022-12-23 08:56:10 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1363719879  

I fixed the bugs a few hours ago and rebased this PR that's why the errors disappeared, you can implement your code now.  
  
Do not worry about coverage for now, coverage measurements are flaky. You found the right script, but it is not tested on Windows, so probably does not work. I will update the CONTRIBUTE.md. You also figured out how to install gcc on Windows, very good, but you can also compile with msvc. Only for coverage you need gcc. gcc-8 is known to work, but gcc-12 (I tested yesterday) also works. You need a matching version of gcov (gcov-12) for it to work, this is important if you have several different versions of gcc installed. The script tools/cov.py is not smart enough to pick the matching version of gcov.  
  
b2filt may or may not work on Windows, it is not tested on Windows.

---

## Comment 3

> Username: HDembinski  
> Created_at: 2022-12-23 10:34:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1363830572  

@jhcarl0814 I found a cross-platform implementation of prefetch in Boost.Context (it often pays off to mine Boost code for hidden gems). I added that so that we can experiment with it. On my platform, prefetch does not improve performance.

---

## Comment 4

> Username: jhcarl0814  
> Created_at: 2022-12-23 20:45:58 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364324583  

@HDembinski That's cool! At least the missing part is filled. Maybe there are other scenarios or platforms that will benefit from it, but we don't know yet. We'll see.

---

## Comment 5

> Username: jhcarl0814  
> Created_at: 2022-12-23 23:08:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364377215  

@HDembinski Sorry to trouble you, this is my first time to do PR.  
  
The change (has passed `b2filt cxxstd=latest warnings-as-errors=on test -j4` and `b2filt cxxstd=latest examples -j4`) has been pushed to https://github.com/jhcarl0814/histogram/tree/eytzinger_search 's `eytzinger_search` branch.  
  
The forking_repo's `eytzinger_search` branch is one commit ahead of https://github.com/boostorg/histogram 's `eytzinger_search` branch.  
  
Should I `git push origin eytzinger_search` or `git fetch origin develop; git merge develop; git push origin eytzinger_search` or ...?  
  
![image](https://user-images.githubusercontent.com/5171262/209412103-482d938d-8f8b-4efe-906c-7665224aeb6c.png)  
  
The git remote config is like this:  
```  
eajniah@jhcarl0814_1:/mnt/c/boost/libs/histogram$ git branch -a  
  develop  
* eytzinger_search  
  remotes/forking_repo/develop  
  remotes/forking_repo/exact_regular  
  remotes/forking_repo/experimental_index_transform  
  remotes/forking_repo/eytzinger_search  
  remotes/forking_repo/fix_clang5  
  remotes/forking_repo/fixes_for_latest_compilers  
  remotes/forking_repo/master  
  remotes/forking_repo/multi_weight  
  remotes/forking_repo/remove_internal_span  
  remotes/forking_repo/unsafe_access_accumulators  
  remotes/origin/HEAD -> origin/develop  
  remotes/origin/develop  
  remotes/origin/exact_regular  
  remotes/origin/experimental_index_transform  
  remotes/origin/eytzinger_search  
  remotes/origin/fix_clang5  
  remotes/origin/master  
  remotes/origin/multi_weight  
  remotes/origin/remove_internal_span  
  remotes/origin/unsafe_access_accumulators  
eajniah@jhcarl0814_1:/mnt/c/boost/libs/histogram$ git remote -v  
forking_repo    https://ghp_...@github.com/jhcarl0814/histogram.git (fetch)  
forking_repo    https://ghp_...@github.com/jhcarl0814/histogram.git (push)  
origin  https://ghp_...@github.com/boostorg/histogram.git (fetch)  
origin  https://ghp_...@github.com/boostorg/histogram.git (push)  
```  
  
---  
  
The default layout_and_algorithm is controled by default type template parameter in `boost::histogram::axis::`  
include\boost\histogram\fwd.hpp  
```C++  
struct sorted_array_and_binary_search{};  
struct eytzinger_layout_and_eytzinger_binary_search{};  
template <class Value = double, class MetaData = use_default, class Options = use_default,  
          class Allocator = std::allocator<Value>, class DataStructure = eytzinger_layout_and_eytzinger_binary_search>  
class variable;  
```  
  
The actual code of each layout_and_algorithm is in `boost::histogram::detail::`  
include\boost\histogram\detail\eytzinger_search.hpp  
```C++  
template <typename T = double>  
struct sorted_array_and_binary_search {...};  
template <typename T = double>  
struct eytzinger_layout_and_eytzinger_binary_search {...};  
```

---

## Comment 6

> Username: jhcarl0814  
> Created_at: 2022-12-23 23:17:42 UTC  
> Updated_at: 2022-12-23 23:34:54 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364386000  

There are some other very small problems found when reading the code:  
  
# 1  
  
`boost::histogram::axis::variable` replaces `boost::use_default` type template parameters (with default types) inside class body. The problem is: we get different types, with exactly same behavior.  
  
- Users might find it's not possible to "determine whether two variable have exactly same behavior" by using `std::is_same_v<decltype(...),decltype(...)>` and have to take on the burden themselves (e.g. add a `xxx_traits` system).  
- The complexity also leaks to complicate those functions that receives `boost::histogram::axis::variable` (e.g. assignment operators and comparison operators).  
- Template deduction deduces to many `boost::use_default` instead of the more useful actually used types. Debugger shows many `boost::use_default` instead of the more useful actually used types.  
  
It would be better to let user use a type alias and seldom use the class template name, so the conditional operations can terminate at the type alias and does not propagate further.  
```C++  
namespace boost  
{  
    namespace histogram  
    {  
        namespace axis  
        {  
//            template<class Value = double, class MetaData = use_default, class Options = use_default, class Allocator = std::allocator<Value>, class DataStructure = eytzinger_layout_and_eytzinger_binary_search>  
//            using variable_t = boost::histogram::axis::variable<  
//              std::conditional_t<std::is_same_v<Value, use_default>, double, Value>,  
//              std::conditional_t<std::is_same_v<MetaData, use_default>, std::string, MetaData>,  
//              std::conditional_t<std::is_same_v<Options, use_default>, decltype(option::underflow | option::overflow), Options>,  
//              std::conditional_t<std::is_same_v<Allocator, use_default>, std::allocator<std::conditional_t<std::is_same_v<Value, use_default>, double, Value>>, Allocator>,  
//              std::conditional_t<std::is_same_v<DataStructure, use_default>, eytzinger_layout_and_eytzinger_binary_search, DataStructure>  
//              >;  
              
            template<class Value = double, class MetaData = use_default, class Options = use_default, class Allocator = std::allocator<Value>, class DataStructure = eytzinger_layout_and_eytzinger_binary_search>  
            using variable_t = boost::histogram::axis::variable<  
              detail::replace_default<Value,double>,  
              detail::replace_default<MetaData,std::string>,  
              detail::replace_default<Options,decltype(option::underflow | option::overflow)>,  
              detail::replace_default<Allocator,std::allocator<detail::replace_default<Value,double>>>,  
              detail::replace_default<DataStructure,eytzinger_layout_and_eytzinger_binary_search>  
              >;  
        }  
    } // namespace histogram  
} // namespace boost  
static_assert(!std::is_same_v<boost::histogram::axis::variable<double, std::string, boost::use_default>, boost::histogram::axis::variable<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)>>);  
static_assert(std::is_same_v<boost::histogram::axis::variable_t<double, std::string, boost::use_default>, boost::histogram::axis::variable_t<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)>>);  
```  
  
# 2  
  
`template <class It, class = detail::requires_iterator<It>> variable(It begin, It end, metadata_type meta = {}, options_type options = {}, allocator_type alloc = {})` does not provide strong exception guarantee: if user writes `variable(it_begin, it_end, std::move(str))` and constructor body throws then the value inside `str` is lost (the value was moved into parameter and then data member before the exception). So one can not write `while(std::cin>>str>>...){ try{ variable(..., ..., str); ... }catch(...){} }` and has to take on the burden of saving the `str` elsewhere (so the user do not have to type it again) when other constructor arguments might be illegal and have to be typed again.  
  
# 3  
  
The `if (options_type::test(option::circular))` inside `index_type index(value_type x)` and `value_type value(real_index_type i)` reminds me of when I was dealing with periodic argument reduction in a simulator+monitor program (every ms PC sends sin(A*t+B) and receives actual value to&from a chip through UDP, t can be as large as several days). There should be some high precision functions in boost.core/utility/utilities/tool/tools/toolbox/... and we just need to find them.  
https://en.cppreference.com/w/cpp/numeric/lerp  
https://stackoverflow.com/questions/64058564/single-precision-argument-reduction-for-trigonometric-functions-in-c  
```C++  
{  
    // using A = axis::variable<double, axis::null_type, op::circular_t>; // using double solves the errors  
    using A = axis::variable<float, axis::null_type, op::circular_t>;  
    auto a = A({  
      1.,  
      1e+8,  
      2e+8,  
    });  
    BOOST_TEST_EQ(a.index(1e8), 1);  
    BOOST_TEST_EQ(a.index(2e8), 0); // test 'a.index(2e8) == 0' ('-1' == '0') failed  
    BOOST_TEST_EQ(a.index(4e8), 0); // test 'a.index(4e8) == 0' ('-1' == '0') failed  
}  
{  
    // using A = axis::variable<double, axis::null_type, op::circular_t>; // using double solves the errors  
    using A = axis::variable<float, axis::null_type, op::circular_t>;  
    auto a = A({  
      -2e+8,  
      -1e+8,  
      -1.,  
    });  
    BOOST_TEST_EQ(a.index(-1e8), 1);  
    BOOST_TEST_EQ(a.index(-2e8), 0);  
    BOOST_TEST_EQ(a.index(-4e8), 1); // test 'a.index(-4e8) == 1' ('0' == '1') failed  
}  
return boost::report_errors();  
```

---

## Comment 7

> Username: HDembinski  
> Created_at: 2022-12-24 00:02:16 UTC  
> Updated_at: 2022-12-24 00:16:15 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364415605  

Your points 1 to 3 sound interesting, but I do not fully understand you yet.  
  
1) The idea behind `boost::use_default` is that the default can be changed locally at the implementation site. It is an established pattern in boost, but perhaps a bit dated. I am not sure what you specifically propose to change. The two blocks of code, the commented and uncommented one, look the same to me. `detail::replace_default` is syntactic sugar for `std::conditional_t<std::is_same_v<Value, use_default> ... >`.  
  
2) ~~How would you provide the strong exception guarantee? In general, we do not know how MetaData is implemented, when it is user-defined.~~ I think I get it now, to achieve this we have to perform `std::move(meta)` at the end of the constructor, after the potential throws. Good catch!  
  
3) If there is a way to make it more accurate, that would be another interesting patch. Regarding std::lerp and std::midpoint, if you want to use C++20 features, we need to wrap the calls and emulate them in C++14. The best is to use some existing code in Boost which already does this, like you said.  
  
But let's not get sidetracked. We should discuss eytzinger here, feel free to open issues for the other topics so that we can discuss them separately.

---

## Comment 8

> Username: HDembinski  
> Created_at: 2022-12-24 00:18:35 UTC  
> Updated_at: 2022-12-24 00:38:10 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364419535  

The failure on Windows is caused by the new detail/prefetch.hpp code. It looks like I oversimplified the original implementation, this needs a better platform check. **Edit**: The original code seemed to be wrong?! Anyway, it works now.

---

## Comment 9

> Username: HDembinski  
> Created_at: 2022-12-24 00:35:47 UTC  
> Updated_at: 2022-12-24 00:42:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364422906  

> Should I `git push origin eytzinger_search` or `git fetch origin develop; git merge develop; git push origin eytzinger_search` or ...?  
  
I just made another commit to this branch to fix the failure on Windows, please rebase your branch (or don't if you already fixed that in your branch). Then sync your local branch to your fork on GitHub. Then browse to your fork on the Github website and use the website interface to start a pull request onto the develop branch of boostorg/histogram. This should create another PR. I should be able to make changes to your PR and we can close this PR then.

---

## Comment 10

> Username: jhcarl0814  
> Created_at: 2022-12-24 06:03:49 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364469071  

@HDembinski The point 1 intends to:  
  
1. let users use `variable_t` when possible as it maps different input types to same output type to make post processing easier;  
2. `variable` should appear only when they want to:  
    - template deduce the type template arguments (`V, M, O, A, ...`) in `variable<V, M, O, A, ...>` or  
    - check if a type is a specialization of `variable`  
  
(there are two implementations of `variable_t` and I commented out one of them, which one is used doesn't matter)  
  
We can ignore this for now because it's not a big issue and it's too easy for users to add this layer of abstraction themselves.

---

## Comment 11

> Username: jhcarl0814  
> Created_at: 2022-12-24 06:08:15 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364469528  

The pull request is created at https://github.com/boostorg/histogram/pull/376 . (The design is very strange because a shallow type and a stateful type are occupying the same position and should be made interchangable.) I will wait for the checks and fix the syntax/semantic errors first.

---

## Comment 12

> Username: HDembinski  
> Created_at: 2022-12-24 08:38:05 UTC  
> Updated_at: 2022-12-24 08:39:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364488748  

> let users use variable_t when possible as it maps different input types to same output type to make post processing easier;\nvariable should appear only when they want to:\ntemplate deduce the type template arguments (V, M, O, A, ...) in variable\u003CV, M, O, A, ...> or\ncheck if a type is a specialization of variable  
  
I still do not understand what your template alias achieves that the current implementation does not. The library was designed so you do not have to specify the template arguments if you are ok with the defaults. In C++14, you can use  
```  
variable<> my_axis;  
```  
To get the defaults. In C++17, you can drop the <> and let the deduction guides select the right type.  
  
What advantages does your idea have over this scheme? What kind of additional abstraction is needed?

---

## Comment 13

> Username: jhcarl0814  
> Created_at: 2022-12-24 09:06:55 UTC  
> Updated_at: 2022-12-24 10:30:47 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364494250  

@HDembinski Here are some examples why using `variable_t` can bring convenience.  
  
```C++  
std::is_same_v<  
    boost::histogram::axis::variable<double, std::string, boost::use_default>,  
    boost::histogram::axis::variable<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)>  
>  
```  
is `false`,  
```C++  
std::is_same_v<  
    boost::histogram::axis::variable_t<double, std::string, boost::use_default>,  
    boost::histogram::axis::variable_t<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)>  
>  
```  
is `true`;  
  
---  
  
This causes a compile error:  
```C++  
boost::histogram::axis::variable<double, std::string, boost::use_default> a;  
boost::histogram::axis::variable<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)> b;  
a = b;  
```  
This does not cause a compile error:  
```C++  
boost::histogram::axis::variable_t<double, std::string, boost::use_default> a;  
boost::histogram::axis::variable_t<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)> b;  
a = b;  
```  
  
---  
  
In templates, you can get the meta type, options type, allocator type conveniently if template users write `variable_t`, not if template users write `variable`:  
```  
template<typename V, typename M, typename O, typename A>  
void f(boost::histogram::axis::variable<V, M, O, A>)  
{  
    std::cout << typeid(V).name() << '\n';  
    std::cout << typeid(M).name() << '\n';  
    std::cout << typeid(O).name() << '\n';  
    std::cout << typeid(A).name() << '\n';  
}  
  
int main(int argc, char *argv[])  
{  
    f(boost::histogram::axis::variable<>());  
    //double  
    //struct boost::use_default  
    //struct boost::use_default  
    //class std::allocator<double>  
  
    f(boost::histogram::axis::variable_t<>());  
    //double  
    //class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >  
    //struct boost::histogram::axis::option::bitset<3>  
    //class std::allocator<double>  
}  
```  
  
---  
  
In debugger, you can not see what options `a` has but can see what options `b` has:  
![image](https://user-images.githubusercontent.com/5171262/209428440-790b9979-b09b-475a-b231-45c61ae69506.png)  
```C++  
boost::histogram::axis::variable<> a;  
boost::histogram::axis::variable_t<> b;  
```

---

## Comment 14

> Username: HDembinski  
> Created_at: 2022-12-24 11:52:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/372#issuecomment-1364517030  

This PR is continued in #376

---
