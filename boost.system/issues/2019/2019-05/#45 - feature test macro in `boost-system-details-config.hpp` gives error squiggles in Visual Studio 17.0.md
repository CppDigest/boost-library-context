# #45 - feature test macro in `boost/system/details/config.hpp` gives error squiggles in Visual Studio 17.0 [Closed]

> Username: lanyizi  
> Created at: 2019-05-12 00:04:08 UTC  
> Updated at: 2020-04-26 14:07:46 UTC  
> Closed at: 2019-05-12 02:35:39 UTC  
> Url: https://github.com/boostorg/system/issues/45  

Visual Studio 2017 15.9.11, platform toolset version v141  
Version of Boost: 1.70.0 (Installed through NuGet Package Manager)  
  
In file `...\packages\boost.1.70.0.0\lib\native\include\boost\system\detail\config.hpp`, line 51,  
`#if __has_cpp_attribute(clang::require_constant_initialization)` will let Visual Studio's Intellisense to trigger error `E2512: Argument for a feature-test-macro has to be a simple identifier`.  
  
[On StackOverflow](https://stackoverflow.com/a/54025978/4399840) somebody suggested that  
> I think that the trouble arises from this VS version introducing some version of `__has_cpp_attribute` which the boost authors expected only to be present in clang.  
  
And he "avoided" the error squiggle by changing  
`#if defined(__has_cpp_attribute)`  
to  
`#if defined(__clang__) && defined(__has_cpp_attribute)`

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-12 00:40:36 UTC  
> Url: https://github.com/boostorg/system/issues/45#issuecomment-491554607  

Intellisense is wrong. The argument to `__has_cpp_attribute` is not a simple identifier, it's an `attribute-token`, which can be either an identifier or `identifier` `::` `identifier`.  
  
But since VS2019 still flags it, I suppose it will be easier for me to just add the `__clang__` check instead of arguing.

---

## Comment 2

> Username: ongunarisev  
> Created at: 2019-08-13 23:58:28 UTC  
> Url: https://github.com/boostorg/system/issues/45#issuecomment-521052255  

I came across this GitHub issue while doing a web search. How did you circumvent the issue exactly, can you be more specific?

---

## Comment 3

> Username: pdimov  
> Created at: 2019-08-14 07:36:49 UTC  
> Url: https://github.com/boostorg/system/issues/45#issuecomment-521137134  

You can see how in the linked commit, https://github.com/boostorg/system/commit/bfbc5ec42ff35f4dcbd1547244504d4e6fc7737d. Since the use of the attribute was no longer necessary after changes done to that part of the code, I just removed it.

---

## Comment 4

> Username: Areg-Abgaryan  
> Created at: 2020-04-26 14:07:46 UTC  
> Url: https://github.com/boostorg/system/issues/45#issuecomment-619556113  

> Visual Studio 2017 15.9.11, platform toolset version v141  
> Version of Boost: 1.70.0 (Installed through NuGet Package Manager)  
>   
> In file `...\packages\boost.1.70.0.0\lib\native\include\boost\system\detail\config.hpp`, line 51,  
> `#if __has_cpp_attribute(clang::require_constant_initialization)` will let Visual Studio's Intellisense to trigger error `E2512: Argument for a feature-test-macro has to be a simple identifier`.  
>   
> [On StackOverflow](https://stackoverflow.com/a/54025978/4399840) somebody suggested that  
>   
> > I think that the trouble arises from this VS version introducing some version of `__has_cpp_attribute` which the boost authors expected only to be present in clang.  
>   
> And he "avoided" the error squiggle by changing  
> `#if defined(__has_cpp_attribute)`  
> to  
> `#if defined(__clang__) && defined(__has_cpp_attribute)`  
  
thx a lot
