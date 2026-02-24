# #240 - Build error on x64 macOS [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-05-28 17:17:36 UTC  
> Updated at: 2025-07-14 01:59:38 UTC  
> Closed at: 2025-07-13 21:16:42 UTC  
> Url: https://github.com/boostorg/parser/issues/240  

See this job: https://github.com/LegalizeAdulthood/asmtk-example/actions/runs/15290710427/job/43009517453  
  
From the error log:  
```  
[100%] Linking CXX executable test-formula  
ld: Undefined symbols:  
  boost::charconv::from_chars(char const*, char const*, double&, boost::charconv::chars_format), referenced from:  
      bool boost::parser::detail::numeric::boost_charconv::parse_real<char const*, char const*, double>(char const*&, char const*, double&) in libformula.a[2](formula.cpp.o)  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
I'm using the `macos-13` runner because another dependency I'm using from vcpkg (asmtk) doesn't build on ARM, so the `macos-latest` runner fails to build.  
  
Any suggestions on how I can work around this?  
  
The `boost-parser` package in vcpkg already lists `boost-charconv` as a dependency: https://vcpkg.io/en/package/boost-parser

---

## Comment 1

> Username: LegalizeAdulthood  
> Created at: 2025-05-28 17:18:25 UTC  
> Url: https://github.com/boostorg/parser/issues/240#issuecomment-2917068176  

It's only the macOS build that is failing; the Windows and linux build (`windows-latest`, `ubuntu-latest`) succeed.

---

## Comment 2

> Username: tzlaine  
> Created at: 2025-07-13 21:16:42 UTC  
> Url: https://github.com/boostorg/parser/issues/240#issuecomment-3067305955  

I don't know what's causing that.  It's a link error, so there's nothing I can do to help you, code-wise.  FWIW, the MacOS-13 target build fine for me in the Boost.Parser CI.  Maybe look there?

---

## Comment 3

> Username: LegalizeAdulthood  
> Created at: 2025-07-14 01:59:38 UTC  
> Url: https://github.com/boostorg/parser/issues/240#issuecomment-3067531420  

OK, I'll look at the boost.parser CI, good suggestion.  Thanks.
