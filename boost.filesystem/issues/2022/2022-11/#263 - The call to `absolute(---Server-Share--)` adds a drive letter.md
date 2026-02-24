# #263 - The call to `absolute("\\Server\Share\")` adds a drive letter [Closed]

> Username: emmenlau  
> Created at: 2022-11-28 20:13:10 UTC  
> Updated at: 2022-12-02 20:30:26 UTC  
> Closed at: 2022-12-02 20:30:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/263  

I'm not sure if this is a bug, but it is causing me problems. I'm calling ` ::boost::filesystem::absolute("\\192.168.33.180\\DataStore\\Folder\\Folder").string()`  and this returns "C:\\192.168.33.180\\DataStore\\Folder\\Folder". I'm not sure if it is legal to call `absolute` with an UNC path?  
  
But if it is legal, I would have expected that the path is returned unmodified.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-11-29 16:53:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/263#issuecomment-1330957637  

What Boost version and Boost.Filesystem version (v3 or v4) are you using?

---

## Comment 2

> Username: emmenlau  
> Created at: 2022-11-29 19:10:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/263#issuecomment-1331168337  

I'm using boost 1.80.0 official release, compiled from sources with clang-cl 15.0.5 frontend for Visual Studio 2022 (current latest), and Boost.Filesystem version v3.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-12-02 20:30:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/263#issuecomment-1335813327  

In your test case `::boost::filesystem::absolute("\\192.168.33.180\\DataStore\\Folder\\Folder").string()`, the leading back slashes are not escaped. That is, the path you're transforming is "\\192.168.33.180\\DataStore\\Folder\\Folder", which is a path relative to the current drive (the "192.168.33.180" is interpreted as a directory name). If you properly escape the leading slashes, the call works as expected.
