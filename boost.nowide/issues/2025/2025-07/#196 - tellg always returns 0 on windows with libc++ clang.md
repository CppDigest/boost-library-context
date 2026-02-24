# #196 - tellg always returns 0 on windows with libc++ clang [Closed]

> Username: GrinlexGH  
> Created at: 2025-07-02 15:14:53 UTC  
> Updated at: 2025-07-05 18:56:10 UTC  
> Closed at: 2025-07-05 18:55:59 UTC  
> Url: https://github.com/boostorg/nowide/issues/196  

on windows tellg always returns 0 with libc++ and clang:  
  
```cpp  
    nowide::ifstream file(path.c_str(), std::ios_base::ate | std::ios_base::binary);  
    file.exceptions(std::ios_base::failbit | std::ios_base::badbit);    // no exceptions throw, but tellg will return 0  
  
    file.seekg(0, std::ios::end);  
    std::streampos pos = file.tellg();  
    file.seekg(0, std::ios::beg);  
    std::cout << "tellg: " << pos << "\n";    // prints 0  
  
    std::ostringstream ss;  
    ss << file.rdbuf();  
    std::cout << ss.str();                   // prints right file contents  
```  
  
  
if I remove these lines from cmake:  
```cmake  
        add_compile_options(  
            "-stdlib=libc++"  
        )  
        add_link_options(  
            "-stdlib=libc++"  
        )  
```  
everything starts work fine

---

## Comment 1

> Username: Flamefire  
> Created at: 2025-07-02 15:59:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/196#issuecomment-3028411432  

Have you tried it with `std::ifstream`?  I think this might be a limitation (or even bug) in libc++.

---

## Comment 2

> Username: GrinlexGH  
> Created at: 2025-07-02 16:07:45 UTC  
> Url: https://github.com/boostorg/nowide/issues/196#issuecomment-3028435961  

> Have you tried it with `std::ifstream`? I think this might be a limitation (or even bug) in libc++.  
  
yes, Ive tried. with `std::ifstream` everything works fine

---

## Comment 3

> Username: Flamefire  
> Created at: 2025-07-03 11:22:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/196#issuecomment-3031906940  

Could you share your minimal CmakeLists and source file if you have it at hand? That would save some time creating a reproducer for this.  
Thanks!

---

## Comment 4

> Username: GrinlexGH  
> Created at: 2025-07-03 15:39:20 UTC  
> Url: https://github.com/boostorg/nowide/issues/196#issuecomment-3032741146  

> Could you share your minimal CmakeLists and source file if you have it at hand? That would save some time creating a reproducer for this. Thanks!  
  
I'm sorry, I haven't been home for so long. here is a minimal project  
  
[nowide-issue-196.zip](https://github.com/user-attachments/files/21040706/nowide-issue-196.zip)

---

## Comment 5

> Username: Flamefire  
> Created at: 2025-07-03 18:12:56 UTC  
> Url: https://github.com/boostorg/nowide/issues/196#issuecomment-3033140243  

Ok thanks. Which exact clang and libc++  are you using? Where is it from?  
Doesn't look like you are using MinGW, are you?

---

## Comment 6

> Username: GrinlexGH  
> Created at: 2025-07-03 21:08:18 UTC  
> Updated at: 2025-07-03 21:09:07 UTC  
> Url: https://github.com/boostorg/nowide/issues/196#issuecomment-3033661134  

> Ok thanks. Which exact clang and libc++ are you using? Where is it from? Doesn't look like you are using MinGW, are you?  
  
I use msys2 with `mingw-w64-ucrt-x86_64-clang` `mingw-w64-ucrt-x86_64-libc++` packages

---

## Comment 7

> Username: Flamefire  
> Created at: 2025-07-05 18:55:59 UTC  
> Updated at: 2025-07-05 18:56:10 UTC  
> Url: https://github.com/boostorg/nowide/issues/196#issuecomment-3039717964  

Thanks, that helped reproduce it with the provided CML.  
  
The issue isn't with Nowide, but caused by mixing libstdc++ (used for building nowide) and libc++ (used for building the binary and linking)  
  
The order of some fields is different between those:  
https://github.com/gcc-mirror/gcc/blob/6c472b330ac4fc0dfbc9cf985e08923abc5bd530/libstdc%2B%2B-v3/include/bits/postypes.h#L90-L91  
https://github.com/llvm/llvm-project/blob/430c0376c8a0bc427d8de09d6af2a68e6bf93caa/libcxx/include/__ios/fpos.h#L25-L26  
  
So it basically reads what was the state field which is set to zero  
  
Moving your `add_link_options` to the top (above `add_subdirectory`) makes it succeed.
