# #88 - boost::filesystem::path diverged from std::filesystem::path [Closed]

> Username: Trafo  
> Created at: 2018-10-30 20:33:16 UTC  
> Updated at: 2021-06-06 19:20:13 UTC  
> Closed at: 2021-06-06 15:48:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88  

Hi,  
  
i realized that the behaviour of boost::filesystem::path and std::filesystem::path are strongly different. For example boost::filesystem::path adds and returns very often ".". Two examples:  
1.) boost::fs::path("foo/bar/").filename() = "." != std::fs::path("foo/bar/").filename() = ""  
2.) boost::fs::path("foo/bar/").lexically_normal() = "foo/bar/." != std::fs::path("foo/bar/").lexically_normal() = "foo/bar"  
  
Why boost::filesystem has this obsession with '.'. Especially in case of a normalization function, we should pick the shortest path representation.  
  
Another question is, why in case of boost::fs::path("/").filename() the method returns "/"? The code handles explicitly this case. (boost::fs::path("/foo/").filename() == ".") Why?  
  
Another question is, why boost::fs::path("").lexically_normal() is violating half of the C++ standard rules? Is their a reason that in case of boost::fs::path("foo/") a '.' will be append after normalization? or that "./foo" is still "./foo" and not "foo"?  
  
I ask this questions, because i am thinking to change the code. But i am afraid, that this behaviour of this code is needed somewhere else.  
  
Best regards,  
Trafo

---

## Comment 1

> Username: pdimov  
> Created at: 2018-10-30 20:56:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-434466332  

When Boost.Filesystem was written, there were no C++ "standard rules" yet, so it can't have violated them.  
  
For `foo/bar/`, I suspect the reason it normalizes to `foo/bar/.` and not to `foo/bar` is that the two are not the same when `bar` is a symbolic link. `foo/bar` refers to the link, and `foo/bar/.` refers to the directory to which `bar` points.  
  
This behavior was probably changed as part of the standardization, and Boost.Filesystem has not been updated to match.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-10-30 20:56:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-434466452  

Boost.Filesystem predates std::filesystem by many years, obviously some design decisions were different when Boost.Filesystem was initially written. You can find some insight on design decisions in the library documentation, although this may not necessarily answer your questions.  
  
I think, @Beman intended to update Boost.Filesystem for better compatibility with the standard library but didn't have the time to do it. In the long run, I think, better compatibility is desired, but not an absolute requirement if there are reasons for the difference.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-10-30 21:09:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-434470513  

In general, "foo/bar/" URI path, with the trailing slash, indeed refers to the location inside the "bar" directory, so the interpretation "foo/bar/." seems correct to me. Also, an empty filename doesn't look valid to me. The URI path "foo/bar", without the trailing slash, identifies the "bar" file. I'm not very familiar with the standard rules or the rationale behind them, so I can't comment on whether the behavior for std::filesystem described in the OP is correct and required by the standard.

---

## Comment 4

> Username: Trafo  
> Created at: 2018-10-30 22:16:42 UTC  
> Updated at: 2018-10-30 22:18:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-434490161  

> When Boost.Filesystem was written, there were no C++ "standard rules" yet, so it can't have violated them.  
  
I know that. And sorry if i choose to offensive words. What i mean is, that i find the standard rules much better and i was wondering why it is different to boost. You have a point with the symbolic link. But i really see no advantages with that, only disadvantages. (look to https://github.com/boostorg/filesystem/issues/76) And what is with this example : <code>fs::path("/foo/./bar/baz").lexically_normal() == "/foo/bar/baz"</code>. Here we could have the same arguments. If foo is a symlink to  "a/b/c", this could points to a/b/c/bar/baz. But like the method name suggest, the normalization is lexically. And because of that symlinks should not matter.  
  
But their still a lot of things inconsistent. For example <code>fs::path("/").filename() == "/"</code>. Because "/" is not a valid filename.  
  
@Lastique   
So it makes no sense, if i open a pull request, if somebody else is already working with another vision then me?  
  
> In general, "foo/bar/" URI path, with the trailing slash, indeed refers to the location inside the "bar" directory, so the interpretation "foo/bar/." seems correct to me.  
  
I disagree here. A Normalization function should return the shortest path to a object inside of the filesystem. For this purpose Normalization functions should exist. And "foo/bar/." reference to bar what is equal to "foo/bar". But second path is shorter. Even in case of a symlink, "foo/bar" is referencing to the same place like "foo/bar/."  
  
> Also, an empty filename doesn't look valid to me.  
  
Okay, but it is much better then '/'. And if boost::filesystem::path would be consistent, it should return '.'. Or did i miss something here?

---

## Comment 5

> Username: pdimov  
> Created at: 2018-10-30 23:09:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-434503264  

Yes, the standard does mandate that `filename()` is `""` for `foo/bar/`: http://eel.is/c++draft/fs.path.decompose#7

---

## Comment 6

> Username: Trafo  
> Created at: 2018-10-31 08:12:19 UTC  
> Updated at: 2018-10-31 08:20:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-434597363  

Another question. Is it right, that you can not calculate the distance between <code>boost::filesystem::path::iterator</code> (directly because <code>std::distance(end(), begin())</code> will fail)? Or that no operator< is defined even we have the <code>m_pos</code> value? But the <code>m_pos</code> value is not accessible from the outside. Or did i miss something and boost have a special iterator treatment?

---

## Comment 7

> Username: Trafo  
> Created at: 2018-11-01 16:39:24 UTC  
> Updated at: 2018-11-01 19:55:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-435102621  

Their are so much more wrong states from my point of view. So my question again, make it sense, if i rewrite path class? Or is it a waste of time for me, because other people already work on it. Because i found other scary things, or other components are used to the wrong output? It should not be to complicated. Because writing tests for the expected behaviour and then go for it.  
  
After normalization -> <code>C:\\..\\..\\foo\\bar </code> could happens. Or <code>boost::fs::path(".hidden").extension() == ".hidden" </code> what is wrong from my point of view. And i found a lot of other things, i not remember anymore.  
  
[EDIT]  
Try to collect other issues here:  
<code> boost::fs::path("C:\\").parent() = "C:" </code> (not valid path)  
<code> boost::fs::path("/").parent() = "" </code> (making a absolute path to an empty relative path)  
<code> boost::fs::path("/").filename() = "/" </code>  
<code> boost::fs::path("//").filename() = "//" </code>  
<code> boost::fs::path("///").filename() = "/" </code>  
<code> boost::fs::path("foo/bar//").filename() = "/" </code> (i already found the issue. It is in filename_pos)  
  
I found the issue inside of the filename_pos method. For me it seems again, that the previous coder did that on purpose. But why? I try at the weekend to open a pull request. It will my first big contribution. Did you have some recommendations for me?

---

## Comment 8

> Username: gri1n  
> Created at: 2019-03-19 10:54:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-474304999  

I just thought I'd chime in and say that I really hope boost::filesystem::path never adopts an implicit conversion to string operator like the c++ standard has.

---

## Comment 9

> Username: jothepro  
> Created at: 2020-06-19 19:07:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-646821152  

I considered using boost::filesystem as a polyfill for std::filesystem, but the implementation looks broken to my untrained eyes:  
```cpp  
// boost 1.72.0  
auto testfolder = boost::filesystem::path("//testfolder").lexically_normal(); // testfolder = "//testfolder"  
auto relativePath = testfolder.lexically_relative("/"); // relativePath = ""  
```  
I'm not super deep into filepath-parsing but this seems wrong to me in every possible way.  
  
In std::filesystem the results make a lot more sense:  
```cpp  
// C++17  
auto testfolder = std::filesystem::path("//testfolder").lexically_normal(); // testfolder = "/testfolder"  
auto relativePath = testfolder.lexically_relative("/"); // relativePath = "testfolder"  
```  
  
I'd also be super interested if this behaviour is there for a reason or if it's a bug that needs fixing. If it is intended then it should be documented, from what the documentation currently says I don't expect this behaviour.  
@Trafo what happened to your attemts to change the implementation?

---

## Comment 10

> Username: Trafo  
> Created at: 2020-06-19 19:19:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-646825515  

From my perspective the boost::filesystem::path is broken in many ways. I wanted to fix the whole thing, but there was no interest in my fixes. (the fixes are quite easy and I guess that are bugs because they look like classical bugs) The only answer I received was, that boost is not std and that somebody is working on changes anyways. And I did not want to work for something, what is build only for the glory. At the end, I wrote my own wrapper to fix all kinds of these bugs. And now I try to use std::filesystem because clang and gcc seem to support it finally as well. And std::filesystem::path works very well.

---

## Comment 11

> Username: Lastique  
> Created at: 2020-06-19 21:43:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-646870895  

I believe, the `//` prefix is interpreted as a [UNC](https://en.wikipedia.org/wiki/Path_(computing)#Universal_Naming_Convention) path prefix.

---

## Comment 12

> Username: jothepro  
> Created at: 2020-06-19 23:40:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-646898686  

Yeah but it obviously isn't a UNC prefix. But I'm not here to fight about the details, I already figured out that `boost::filesystem` does not work as polyfill for `std::filesystem`.  
  
If anybody else reads this because he needs an alternative to c++17 std::filesystem, I found this compatible implementation for older c++-versions: https://github.com/gulrak/filesystem  
  
I've not tested it yet but it sounds pretty promising!

---

## Comment 13

> Username: Trafo  
> Created at: 2020-06-20 03:53:43 UTC  
> Updated at: 2020-06-20 03:54:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-646934747  

The UNC misinterpretation is not the only misinterpretation of boost. My favorite is still ```boost::fs::path(".hidden").extension() == ".hidden"```. (that is only one example) Even windows, where the starting point does not have any impact, ```.hidden``` is not a file extension. And under Unix and macOS it is completely wrong. All these kinds of misinterpretations lead to follow up errors. In this case, it leads only to errors in the case of using ```replace_extension(...)```. But for example, in case of finding not the right root_path/root_name, it causes a lot of other follow up failures in many other functions.  
  
But anyway I am over it. :) And the posted repo looks really promising.

---

## Comment 14

> Username: Lastique  
> Created at: 2021-06-06 15:48:41 UTC  
> Updated at: 2021-06-06 19:20:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/88#issuecomment-855419701  

The `.hidden` filename will be interpreted as a stem (and not an extension) starting with Boost.Filesystem 1.77.  
  
The leading double slashes (forward or backward) are interpreted as a UNC prefix, and this will not change. Triple slashes are interpreted as redundant leading slash (not a UNC prefix followed by a slash). Hence the difference in behavior.  
  
The `C:` path is a valid path, which refers to the current directory on drive `C`. Similarly, `C:foo` is also a valid path that refers to file `foo` in the current directory for drive `C`.  
  
In the `boost::fs::path("/").filename() = "/"` case, filename returns root directory. I'm not sure how valid that is, or what was the rationale behind it, but it seems intentional. Probably worth changing.  
  
The only other issue that I can see mentioned here was the trailing `.` element as the implicit filename. I think, the rationale for this is that iterating over `/foo/bar` and `/foo/bar/` as must not produce equal sequences of path elements. This is documented [here](https://www.boost.org/doc/libs/1_76_0/libs/filesystem/doc/reference.html#path-iterators). Also, the semantic difference that I mentioned earlier, that `/foo/bar` refers to `bar` and `/foo/bar/.` refers to a file *inside* `bar`, is also valid. One consequence of that is in the latter case `bar` must be a directory, while there is no such requirement implied by the former path.  
  
However, I'm not extremely tied to the current behavior. I can see the value in better compatibility with std::filesystem, so I'm not opposed to changing the current behavior. I'll create a separate issue for this.  
  
If I missed some issues mentioned in the discussion, please create separate bugs for those - one bug per issue, with a repro. I'm going to close this issue.  
  
Thanks everyone.
