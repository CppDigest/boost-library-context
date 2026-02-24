# #99 Remove deprecated std::unary_function and std::binary_function from examples [Merged]

> Username: pavelkryukov  
> Created at: 2017-11-20 16:13:25 UTC  
> Updated at: 2018-10-14 08:03:33 UTC  
> Merged at: 2018-10-14 07:48:00 UTC  
> Closed at: 2018-10-14 07:48:00 UTC  
> Url: https://github.com/boostorg/graph/pull/99  

std::binary_function was removed in C++17 and is no longer available while compiling with MS Visual Studio 2017

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:42:41 UTC  
> Url: https://github.com/boostorg/graph/pull/99#issuecomment-417739648  

I'm helping out with the PR backlog.  It may be depreciated but that doesn't mean that it is not of value.  Could you make a strong argument for the change?  This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: pavelkryukov  
> Created_at: 2018-08-31 18:05:38 UTC  
> Updated_at: 2018-08-31 18:14:21 UTC  
> Url: https://github.com/boostorg/graph/pull/99#issuecomment-417746069  

> It may be depreciated but that doesn't mean that it is not of value.   
  
It was _deprecated_ in C++11, now it is _removed_ in C++17, meaning it is no longer valid C++ code.  
  
> is no longer available while compiling with MS Visual Studio 2017  
  
It means the example won't compile with MS Visual Studio 2017 or any newer version.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-08-31 18:56:40 UTC  
> Url: https://github.com/boostorg/graph/pull/99#issuecomment-417759713  

I have a lot of these to read on.  Could you please check for me that no where else in the code needs std::unary_function or std::binary_function?  If not, I think I'll include this is the first batch of merges.

---

## Comment 4

> Username: pavelkryukov  
> Created_at: 2018-08-31 20:16:12 UTC  
> Url: https://github.com/boostorg/graph/pull/99#issuecomment-417777871  

Excuse me, could you please confirm your affiliation with Boost org?  
  
The only things `std::unary_function` and `std::binary_function` had in C++03 were type definitions for `argument_type`, `first_argument_type`, `second_argument_type` and `result_type`, therefore there is no change in behavior as I copied type definitions inside the classes.  
Moreover, it is an example, therefore it is not included to any other file.

---

## Comment 5

> Username: anadon  
> Created_at: 2018-08-31 22:45:12 UTC  
> Url: https://github.com/boostorg/graph/pull/99#issuecomment-417807465  

Here is a link to the thread: https://lists.boost.org/Archives/boost/2018/08/243175.php  
In sum, I'm getting together low hanging fruit and making consolidated pulls which someone trusted with write access will handle.

---

## Comment 6

> Username: pavelkryukov  
> Created_at: 2018-09-02 10:58:14 UTC  
> Url: https://github.com/boostorg/graph/pull/99#issuecomment-417921713  

Reopened pull request to trigger CI builds.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-10-14 07:47:54 UTC  
> Url: https://github.com/boostorg/graph/pull/99#issuecomment-429604281  

Yup, also uncontroversial IMO.  Merging.

---
