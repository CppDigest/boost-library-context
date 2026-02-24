# #48 Add local conanfile.py [Merged]

> Username: kammce  
> Created at: 2022-08-31 15:47:06 UTC  
> Updated at: 2022-09-02 06:16:40 UTC  
> Merged at: 2022-09-02 06:16:39 UTC  
> Closed at: 2022-09-02 06:16:39 UTC  
> Url: https://github.com/boostorg/leaf/pull/48  

- Bump version to 0.4.0

---

## Comment 1

> Username: kammce  
> Created_at: 2022-08-31 16:46:08 UTC  
> Url: https://github.com/boostorg/leaf/pull/48#issuecomment-1233178652  

I will need some help figuring out which compiler versions can support BOOST LEAF  
  
Currently I have this list in the conan file:  
```  
            "gcc": "11",  
            "Visual Studio": "17",  
            "msvc": "193",  
            "clang": "13",  
            "apple-clang": "13.1.6"  
```

---

## Comment 2

> Username: kammce  
> Created_at: 2022-08-31 21:01:07 UTC  
> Url: https://github.com/boostorg/leaf/pull/48#issuecomment-1233416878  

> I will need some help figuring out which compiler versions can support BOOST LEAF  
>   
> Currently I have this list in the conan file:  
>   
> ```  
>             "gcc": "11",  
>             "Visual Studio": "17",  
>             "msvc": "193",  
>             "clang": "13",  
>             "apple-clang": "13.1.6"  
> ```  
  
I used the CI steps to figure this out.

---

## Comment 3

> Username: zajo  
> Created_at: 2022-09-01 05:09:00 UTC  
> Url: https://github.com/boostorg/leaf/pull/48#issuecomment-1233747438  

Re: compiler versions, this is from gha/ci.yml:  
  
```  
          - toolset: gcc-4.8  
            cxxstd: "11"  
            os: ubuntu-18.04  
            install: g++-4.8  
          - toolset: gcc-5  
            cxxstd: "11,14,1z"  
            os: ubuntu-18.04  
            install: g++-5  
          - toolset: gcc-6  
            cxxstd: "11,14,1z"  
            os: ubuntu-18.04  
            install: g++-6  
          - toolset: gcc-7  
            cxxstd: "11,14"  
            os: ubuntu-18.04  
          - toolset: gcc-8  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-18.04  
            install: g++-8  
          - toolset: gcc-9  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-20.04  
          - toolset: gcc-10  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-20.04  
            install: g++-10  
          - toolset: gcc-11  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-20.04  
            install: g++-11  
          - toolset: clang  
            compiler: clang++-3.9  
            cxxstd: "11,14"  
            os: ubuntu-18.04  
            install: clang-3.9  
          - toolset: clang  
            compiler: clang++-4.0  
            cxxstd: "11,14"  
            os: ubuntu-18.04  
            install: clang-4.0  
          - toolset: clang  
            compiler: clang++-5.0  
            cxxstd: "11,14,1z"  
            os: ubuntu-18.04  
            install: clang-5.0  
          - toolset: clang  
            compiler: clang++-6.0  
            cxxstd: "11,14,17"  
            os: ubuntu-18.04  
            install: clang-6.0  
          - toolset: clang  
            compiler: clang++-7  
            cxxstd: "11,14,17"  
            os: ubuntu-18.04  
            install: clang-7  
          - toolset: clang  
            compiler: clang++-8  
            cxxstd: "11,14,17"  
            os: ubuntu-20.04  
            install: clang-8  
          - toolset: clang  
            compiler: clang++-9  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-20.04  
            install: clang-9  
          - toolset: clang  
            compiler: clang++-10  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-20.04  
            install: clang-10  
          - toolset: clang  
            compiler: clang++-11  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-20.04  
            install: clang-11  
          - toolset: clang  
            compiler: clang++-12  
            cxxstd: "11,14,17,2a"  
            os: ubuntu-20.04  
            install: clang-12  
          - toolset: clang  
            cxxstd: "11,14,17,2a"  
            os: macos-10.15  
```  
  
And on Windows:  
  
```  
          - toolset: msvc-14.1  
            cxxstd: "14,17,latest"  
            addrmd: 32,64  
            os: windows-2016  
          - toolset: msvc-14.2  
            cxxstd: "14,17,latest"  
            addrmd: 32,64  
            os: windows-2019  
          - toolset: gcc  
            cxxstd: "11,14,17,2a"  
            addrmd: 64  
            os: windows-2019  
```

---

## Comment 4

> Username: kammce  
> Created_at: 2022-09-01 13:59:32 UTC  
> Url: https://github.com/boostorg/leaf/pull/48#issuecomment-1234321944  

This PR should be ready to be merged in now.

---
