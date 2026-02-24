# #168 - Issue with std::istream::putback() [Closed]

> Username: H5117  
> Created at: 2022-11-14 10:17:26 UTC  
> Updated at: 2022-11-15 10:18:12 UTC  
> Closed at: 2022-11-15 10:18:12 UTC  
> Url: https://github.com/boostorg/nowide/issues/168  

I have got an issue with nowide. The reproducer:  
```c++  
#include <iostream>  
#include <fstream>  
#include <nowide/fstream.hpp>  
  
int main()  
{  
    nowide::ifstream file("file.txt");  
    if (file.good()) {  
      std::cout << "The file is good!" << std::endl;  
    } else {  
      std::cout << "The file is NOT good!" << std::endl;  
    }  
  
    char c1 = file.get();  
    char c2 = file.get();  
  
    file.putback(c2);  
    file.putback(c1);  
  
    if (file.good()) {  
      std::cout << "The file is good!" << std::endl;  
    } else {  
      std::cout << "The file is NOT good!" << std::endl;  
    }  
  
    if (file.rdstate() == std::ios_base::badbit) {  
      std::cout << "The file state is badbit\n";  
    }  
    if (file.rdstate() == std::ios_base::failbit) {  
      std::cout << "The file state is failbit\n";  
    }  
    if (file.rdstate() == std::ios_base::eofbit) {  
      std::cout << "The file state is eofbit\n";  
    }  
  
    file.close();  
    return 0;  
}  
```  
Output:  
```  
The file is good!  
The file is NOT good!  
The file state is badbit  
```  
  
The reproducer works fine with std::ifstream.  
  
Microsoft Visual C++ 2022 v143  
SDK: 10.0.19041.0

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-11-14 11:49:50 UTC  
> Url: https://github.com/boostorg/nowide/issues/168#issuecomment-1313559164  

This is expected: Putback is only possible into the current read buffer after #159 which for text files is a single char.  
  
As per the C++ standard the amount of putback operations is implementation defined so here only the most common use case is handled: putback of the last char extracted and possibly more for binary files.  
  
Reason is the amount of corner cases: Imagine a text file with unknown line endings and you putback a newline. Or any other char over where a newline was. The behavior in such cases may be surprising and dependent on the file and/or OS used. Hence I decided to make the operation fail which is at least predictable.  
  
Note that you can produce nice issues with MSVCs `std::ifstream`: Try reading a file with UNIX line endings (\n) and putback/seek around newlines.

---

## Comment 2

> Username: H5117  
> Created at: 2022-11-14 14:23:35 UTC  
> Url: https://github.com/boostorg/nowide/issues/168#issuecomment-1313801371  

> As per the C++ standard the amount of putback operations is implementation defined  
  
The only limitation I see [here](https://en.cppreference.com/w/cpp/io/basic_streambuf/sputbackc) is that you can't add chars outside of the original buffer. Could you provide the link please?  
  
Such behavior makes nowide unusable with [yaml-cpp](https://github.com/jbeder/yaml-cpp) that uses multiple `putback()`'s for parsing of a BOM [[1]](https://github.com/jbeder/yaml-cpp/blob/1b50109f7bea60bd382d8ea7befce3d2bd67da5f/src/stream.cpp#L200). So seems other ifstream implementations (not only on Windows) allow to do it.  
  
What about `std::istream::unget()`? Is it also limited to a single character?

---

## Comment 3

> Username: Flamefire  
> Created at: 2022-11-14 14:41:44 UTC  
> Url: https://github.com/boostorg/nowide/issues/168#issuecomment-1313861570  

> Could you provide the link please?  
  
Your link says that it calls `pbackfail` which is what happens for text streams after that 1 character. And that [reads](https://en.cppreference.com/w/cpp/io/basic_filebuf/pbackfail)   
  
> backs up the get area by re-reading the file starting one byte earlier.  
  
And this operation is not possible to do (in library code) for text file streams because "one byte earlier" is not defined there. I.e. it would require a `fseek(f, SEEK_CUR, -1)` or similar but you are not allowed to do that for text files and you get surprising results when forcing to do that on Windows. I found that MSVC STL always assumes a `\r\n` and if there only was a `\n` then it will miss a character.  
Hence the decision to not try that.  
  
And yes `unget` also calls `pbackfail` so the same applies.  
  
> Such behavior makes nowide unusable with [yaml-cpp](https://github.com/jbeder/yaml-cpp) that uses multiple putback()'s for parsing of a BOM [[1]](https://github.com/jbeder/yaml-cpp/blob/1b50109f7bea60bd382d8ea7befce3d2bd67da5f/src/stream.cpp#L200)  
  
Can't you pass a binary stream to yaml-cpp?

---

## Comment 4

> Username: artyom-beilis  
> Created at: 2022-11-14 15:39:45 UTC  
> Updated at: 2022-11-14 15:40:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/168#issuecomment-1313951984  

> Such behavior makes nowide unusable with [yaml-cpp](https://github.com/jbeder/yaml-cpp) that uses multiple `putback()`'s for parsing of a BOM [[1]](https://github.com/jbeder/yaml-cpp/blob/1b50109f7bea60bd382d8ea7befce3d2bd67da5f/src/stream.cpp#L200). So seems other ifstream implementations (not only on Windows) allow to do it.  
  
Yaml-CPP provides `std::istream` as interface and it assumes that you can put-back stuff. It is generally incorrect. For example I can pass my own totally unbuffered stream and it just would not work either. `Parser::Parser(std::istream& in)`   
  
I personally suggest to use double buffering in yaml library. Or create internal buffering and don't assume `istream` can handle it. If you `yaml-cpp` maintainer - fix it since it is clear bug, if you are user open a ticket or provide a fix.

---

## Comment 5

> Username: H5117  
> Created at: 2022-11-15 10:18:12 UTC  
> Url: https://github.com/boostorg/nowide/issues/168#issuecomment-1315095084  

Thank you for the answers. I have opened the issue for yaml-cpp.  
  
> Can't you pass a binary stream to yaml-cpp?  
  
I think yes, but automatic conversion of line endings of configuration files is a good thing for a multiplatform project, that uses nowide. I leave it as the last solution.
