# #249 - How to write an efficient `contains` method? [Closed]

> Username: emmenlau  
> Created at: 2022-08-11 13:58:16 UTC  
> Updated at: 2022-08-11 18:31:57 UTC  
> Closed at: 2022-08-11 15:13:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/249  

I'd like to write an efficient method to test if a given (child) path is contained within another (parent) path. I do not need canonical comparison (right now).  
  
My idea was to iterate both paths, and compare the components. But that does not work for me when the parent path ends in a directory separator. I understand that the last element during iteration becomes an empty path. But I do not know how to deal with that.  
  
What is the suggested way to work with path iterator pointing to the empty path? And I'd also happily take advise on writing an efficient `contains()` method. Below is my current working draft.  
  
Here is what I tried. The following method returns false, but I think it should return true:  
```c++  
bool contains(const std::string aParentPath, const std::string& aChildPath) {  
    ::boost::filesystem::path vParentPath{ aParentPath };  
    ::boost::filesystem::path vChildPath{ aChildPath };  
  
    auto vChildIt = vChildPath.begin();  
    for (auto vParentIt = vParentPath.begin(); vParentIt != vParentPath.end(); ++vParentIt, ++vChildIt) {  
        // Here I fail to identify the empty path at the end:  
        if (vParentIt->filename().empty()) {  
            continue;  
        }  
  
        // Is this an efficient comparison? Could it be done better / more efficient?  
        // I do not mean to compare the full sub-paths, only the current component...  
        if (*vParentIt != *vChildIt) {  
            return false;  
        }  
    }  
    return true;  
}  
  
contains("/some/test/path/with/slash/", "/some/test/path/with/slash/and/files.txt");  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-11 15:13:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/249#issuecomment-1212128564  

Re. your code snippet, you need to test `vChildIt` against `vChildPath.end()`.  
  
Comparing path elements is as efficient as comparing strings, there's nothing you could optimize at this level. But extracting path elements during iteration involves parsing the path, and this could be avoided.  
  
If you can guarantee that your input strings have some unified representation (i.e. no double slashes, only one type of element delimiters, etc.), you could get away with just comparing the strings directly. This would save you from parsing the paths during iteration.  
  
```  
bool contains(const std::string& aParentPath, const std::string& aChildPath) {  
    // These two lines are not needed if you can guarantee that aParentPath and aChildPath are already normal  
    std::string vParentPath{ boost::filesystem::path(aParentPath).lexically_normal().string() };  
    std::string vChildPath{ boost::filesystem::path(aChildPath).lexically_normal().string() };  
  
    return vChildPath.starts_with(vParentPath);  
}  
```  
  
I'm not sure if this will be much more efficient.  
  
However, note that whether comparing strings or path elements, equivalent paths containing "." and ".." elements at different locations will compare different.

---

## Comment 2

> Username: emmenlau  
> Created at: 2022-08-11 18:31:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/249#issuecomment-1212350477  

Thanks a lot, this did help a lot!
