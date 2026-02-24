# #99 - Handling of volume designators broken on windows [Closed]

> Username: Flamefire  
> Created at: 2019-02-02 15:22:54 UTC  
> Updated at: 2020-05-02 17:06:02 UTC  
> Closed at: 2020-05-02 17:06:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/99  

I noticed the problem when using `canonical` when I'm currently in a directory with a junction point.  
  
To reproduce:  
- Create junction point: `mklink /J C:/tmp/junction C:/tmp/real`  
- Put an executable into `C:/tmp/real` which calls e.g. `canonical("C:/tmp/real")`  
- Execute once form within `C:/tmp/junction` and `C:/tmp/real`  
- Observe different results while stepping through boost source (see below)  
  
What happens:  
- We are iterating over the path in https://github.com/boostorg/filesystem/blob/b0a0fde6509df970a5387ce8d3ae0d47846f0907/src/operations.cpp#L888  
- The first entry is `"C:"`  
- This is passed into `is_symlink` in https://github.com/boostorg/filesystem/blob/b0a0fde6509df970a5387ce8d3ae0d47846f0907/src/operations.cpp#L901 yielding true when in `junction` and false when in `real`!!!  
  
Reason for that is the call to `GetFileAttributesW` in https://github.com/boostorg/filesystem/blob/b0a0fde6509df970a5387ce8d3ae0d47846f0907/src/operations.cpp#L1901. As described in e.g. https://stackoverflow.com/questions/38300808/why-does-getfileattributeswlc-return-file-attribute-reparse-point `"C:"` does not mean "the drive C" but "the current working directory on drive C" (https://docs.microsoft.com/de-de/windows/desktop/FileIO/naming-a-file)  
  
> If a file name begins with only a disk designator but not the backslash after the colon, it is interpreted as a relative path to the current directory on the drive with the specified letter.  
  
This is a very serious issue as it returns wrong results in unexpected ways.  
  
I'm not sure how to fix this. Maybe iteration should not return the "root_name" but the "root_path" first? Or the root_name on windows shall include the slash if given?  
  
I'm afraid there was a mistake when defining the decomposition table (https://www.boost.org/doc/libs/1_68_0/libs/filesystem/doc/reference.html#path-decomposition-table): for `c:` vs `c:\` (vs `c:/`?): the (back)slash after the colon has to be treated as part of the first element or the meaning is significantly altered. (I'm not 100% sure that `C:\` is the same as `C:/` but it seems like [`GetFileAttributesW` returns the same])

---

## Comment 1

> Username: NicolBolas  
> Created at: 2019-02-02 17:17:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/99#issuecomment-459982132  

This seems more like an implementation issue with `boost::canonical`. That is, it shouldn't be asking if the root name is a sym-link. The decomposition sequence itself is fine, but there is no point in asking if a root name is a symlink.

---

## Comment 2

> Username: Flamefire  
> Created at: 2019-02-03 10:55:47 UTC  
> Url: https://github.com/boostorg/filesystem/issues/99#issuecomment-460041605  

Yes it is indeed. I asked for clarification on the root_name vs root_path vs root_directory on SO: https://stackoverflow.com/a/54495151/1930508  
  
Essence: Not only is there "no point in asking if a root name is a symlink" but it is plain wrong. In extension all other places where `path` components are iterated should be checked too for the same mistake: Unless the path is absolute it might not be what is expected

---

## Comment 3

> Username: Flamefire  
> Created at: 2019-02-04 16:51:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/99#issuecomment-460322983  

@NicolBolas I fixed this in #100 and added test cases for this issue.  
  
I did not found any more potential issues after a quick glance.
