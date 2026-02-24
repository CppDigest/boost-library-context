# #793 - utf8 file names [Closed]

> Username: vinniefalco  
> Created at: 2017-09-21 23:10:09 UTC  
> Updated at: 2020-01-20 13:14:18 UTC  
> Closed at: 2019-12-29 19:20:47 UTC  
> Url: https://github.com/boostorg/beast/issues/793  

Implementations of **File** need to support utf-8 as promised.

---

## Comment 1

> Username: ngladitz  
> Created at: 2017-09-22 06:45:34 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-331364589  

Maybe there could be two distinct overloads like there are in the Windows APIs and e.g. boost::filesystem?  
  
One for `char` using what ever the standard library already uses based on the current locale and on Windows an additional overload for `wchar_t` which (on Windows) would correspond with UTF-16.  
  
In context of boost::filesystem this would portably allow passing `path.c_str()` (for path of type `boost::filesystem::path`) to `open()`.  
  
Otherwise I'd have to convert from the current locale based encoding (or UTF-16) to UTF-8 which Beast would then basically have to revert for the native call.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-09-22 08:08:10 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-331380662  

`wchar_t` is a mess, a straightforward `char const*` is the best choice, although in some cases it is true that there would have to be a double conversion. See: http://utf8everywhere.org/

---

## Comment 3

> Username: mika-fischer  
> Created at: 2019-08-13 10:13:24 UTC  
> Updated at: 2019-08-13 10:26:23 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-520775946  

I don't disagree that the whole `wchar_t` thing on Windows is a mess, but I think what Beast currently does is just broken:  
  
It uses `CreateFileA`, which by design can only open files with names that can be represented in the current codepage. If you want the ability to open *all possible* files, you *need* to use `CreateFileW`.  
  
It is then an orthogonal question on whether beast wants to duplicate the windows API style (`char*` == codepage, `wchar_t*` == unicode), or whether to only accept utf-8. But in the latter case beast needs to convert the utf-8 to UCS2 in order to be able to call `CreateFileW`.  
  
I use this to convert UTF-8 to std::wstring for use in Windows APIs:  
```cpp  
std::wstring mb2wide(std::string_view input) {  
    std::wstring result;  
    result.resize(MultiByteToWideChar(boost::winapi::CP_UTF8_, 0, input.data(), input.size(), nullptr, 0));  
    MultiByteToWideChar(boost::winapi::CP_UTF8_, 0, input.data(), input.size(), result.data(), result.size());  
    return result;  
}  
```  
  
Another thing to consider is support for `std::filesystem::path`, which also does the dual-API thing with `c_str()` (returns `wchar_t*` on Windows, `char*` on Linux)...

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-08-13 14:53:56 UTC  
> Updated at: 2019-08-13 14:54:05 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-520869508  

@mika-fischer That code looks wonderful! If you would like to submit a pull request (consider adding a test ifdefed for Windows as well) I would be happy to merge it. Or I could do it.

---

## Comment 5

> Username: mika-fischer  
> Created at: 2019-08-13 15:14:13 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-520878261  

I won't get around to it until after vacation time (September) but then I can do that.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-08-13 15:18:27 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-520880097  

The next Boost release is in December, so it could easily make it in

---

## Comment 7

> Username: mika-fischer  
> Created at: 2019-12-23 18:40:03 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-568549621  

All right, I got a working prototype. Another question here is, what to do with `file_stdio` on Windows. It is broken in the same way and could receive the same fix via [_wfopen](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/fopen-wfopen?view=vs-2017). Should I also use that on Windows?  
  
Another issue is that on non-Windows platforms, the doc stating that paths are UTF-8 encoded is also strictly wrong, since that is dependent on the locale... But I guess most people use UTF-8 locales nowadays, so that is not that big an issue...

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-12-23 18:47:57 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-568551223  

I'm not a fan of `locale` at all if it can be avoided. If we are going to use it, I would like an explanation of exactly why it is the right choice.

---

## Comment 9

> Username: mika-fischer  
> Created at: 2019-12-23 19:05:22 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-568555494  

First of all, I agree, it's a horrible mess all around...  
  
But it's easy to see what's the problem. Assume you're on a Linux machine and the system locale is set to `de_DE`, which means it uses ISO-8859-1 encoding. This means all conforming applications will encode file names in ISO-8859-1.  
  
Beast docs state that `file_*::open` take exclusively UTF-8 encoded paths.  
  
Now if I want to upload one of those files using beast I run into a problem:  
- When I convert the path from ISO-8859-1 to UTF-8 and then pass it to beast, it won't work, because beast just passes it along to `::open` or `::fopen` which both don't care about the encoding at all and just pass it along to the OS, which errors out because a file with a UTF-8-encoded name does not exist...  
- When I don't convert from ISO-8859-1 and just pass it to beast anyway, it will work, even though the path might not even be *valid* UTF-8...  
  
I don't know of a nice solution for this. I'd just leave it as is at the moment. At some point it might make sense to only support `std::filesystem::path` and let *it* deal with these headaches...

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-12-23 19:11:56 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-568557031  

> When I convert the path from ISO-8859-1 to UTF-8 and then pass it to beast, it won't work, because beast just passes it along to ::open or ::fopen which both don't care about the encoding at all and just pass it along to the OS  
  
Yes, well - that's the bug isn't it? Beast needs to use the correct non-portable platform API that treats the file as utf-8.  
  
> I'd just leave it as is at the moment.  
  
I'm okay with that. I am working on a new library Boost.URL (https://github.com/vinniefalco/url) and I can revisit this issue of paths, since the canonical use-case is to map the request-target in an HTTP request to a local file.  
  
> ...it might make sense to only support std::filesystem::path  
  
There are a few problems with this. First it requires `std::filesystem` (or `boost::filesystem`). I would like Beast to depend on less rather than more. We could alleviate that by putting Beast's _File_ abstractions into their own library, where they belong. Then Beast users who don't need files (most websocket users) won't have to drag in filesystem.  
  
But second, does `filesystem::` even address this? Filesystem deals with path strings, but does it also have an API for actually _opening_ a file and getting a file handle?

---

## Comment 11

> Username: mika-fischer  
> Created at: 2019-12-23 19:34:45 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-568562756  

> Yes, well - that's the bug isn't it? Beast needs to use the correct non-portable platform API that treats the file as utf-8.  
  
AFAIK, there's no such thing on Linux. The kernel just doesn't care at all about the encoding of filenames, it's just bytes arrays to it.  
  
So the easiest way to get by on Linux is to just not care as well and just pass everything through. The only problem then is that the docs are wrong :-)  
  
> There are a few problems with this. First it requires `std::filesystem` (or `boost::filesystem`). I would like Beast to depend on less rather than more. We could alleviate that by putting Beast's _File_ abstractions into their own library, where they belong. Then Beast users who don't need files (most websocket users) won't have to drag in filesystem.  
  
I get that, it's more a long-term "solution"...  
  
> But second, does `filesystem::` even address this? Filesystem deals with path strings, but does it also have an API for actually _opening_ a file and getting a file handle?  
  
Well, its basic premise is that `path.c_str()` gives you something that you can pass to OS functions (on Windows it returns `wchar_t*`, so you can use the unicode variants).  
  
However, it does *not* treat `char*` as UTF-8! On Windows `char*` is supposed to be encoded in the system codepage, and on POSIX, it's supposed to be encoded in the system locale (which might be UTF-8).  
  
Other than that, `boost::filesystem` has `fs::fstream` which bridges filesystem and iostreams. But that's all AFAIK, and I think `std::filesystem` doesn't even have that.

---

## Comment 12

> Username: mika-fischer  
> Created at: 2019-12-23 20:26:36 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-568573738  

I also fixed file_stdio by using `_wfopen_s` in the pull request.

---

## Comment 13

> Username: mika-fischer  
> Created at: 2020-01-20 12:29:56 UTC  
> Url: https://github.com/boostorg/beast/issues/793#issuecomment-576253493  

Thanks for merging this!  
  
Please do note that this is a breaking change for users on Windows, so I would put a big note in the release notes...  
  
Previously, people could just pass the filenames encoded in the current Windows codepage to beast and the would just work. Now, users need to convert them to UTF-8 manually...
