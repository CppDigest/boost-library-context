# #135 - widen and narrow with MSVC C++latest [Closed]

> Username: jimorc  
> Created at: 2021-04-28 18:08:02 UTC  
> Updated at: 2021-04-29 10:41:48 UTC  
> Closed at: 2021-04-28 21:36:51 UTC  
> Url: https://github.com/boostorg/nowide/issues/135  

I am not sure where this problem fits, but changes to nowide would fix it:  
Using MSVC and compiling for C++17, the following code compiles and executes:  
  
```c_cpp  
std::string str = u8"🕳️💚💜";  
std::wstring wide = nowide::widen(str);  
```  
  
If I were to print wide out or display it in a Win32 MessageBox, the correct text is displayed.  
  
If I try this with MSVC C++latest, I must define `str` as:  
```c_cpp  
std::u8string str = u8"🕳️💚💜";  
```  
  
But `nowide::widen` does not accept `u8string`s. I also cannot remove the `u8` at the start of the string because then the compiler complains that the string contains characters that are not in the code page.  
  
Suggestion: Update `nowide::widen` to take `u8string`s as input, and update `nowide::narrow` to output `u8string`s.

---

## Comment 1

> Username: jimorc  
> Created at: 2021-04-28 18:56:35 UTC  
> Url: https://github.com/boostorg/nowide/issues/135#issuecomment-828701652  

I have found that if I change the code page to UTF-8, I can remove the `u8` from the start of the string, and the code works. However, that is not a general solution.

---

## Comment 2

> Username: Flamefire  
> Created at: 2021-04-28 19:47:02 UTC  
> Url: https://github.com/boostorg/nowide/issues/135#issuecomment-828731132  

Accepting an u8string should already be possible, as Nowide should understand u8char: https://github.com/boostorg/nowide/blob/develop/include/boost/nowide/detail/is_string_container.hpp#L43  
Have you tried with the latest Nowide?  
  
As it is impossible to overload a function on the return type the 2nd suggestion of returning an u8string is unfortunately not possible. But you can use the underlying convert_string function: https://github.com/boostorg/nowide/blob/develop/include/boost/nowide/utf/convert.hpp#L75

---

## Comment 3

> Username: jimorc  
> Created at: 2021-04-28 21:36:51 UTC  
> Updated at: 2021-04-28 21:37:19 UTC  
> Url: https://github.com/boostorg/nowide/issues/135#issuecomment-828796281  

Thank you Flamefire for the quick answer. My problem was I downloaded the nowide_standalone version from some page that said to download the latest standalone version, but it pointed to a version from 2017. Now that I have downloaded the latest standalone version from the GitHub repository, the code works.  
  
Thanks again.

---

## Comment 4

> Username: Flamefire  
> Created at: 2021-04-29 10:41:48 UTC  
> Url: https://github.com/boostorg/nowide/issues/135#issuecomment-829125428  

Glad to hear!
