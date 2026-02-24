# #680 - Incorrect port number validation [Closed]

> Username: kenballus  
> Created at: 2023-02-06 04:44:12 UTC  
> Updated at: 2023-02-06 14:54:30 UTC  
> Closed at: 2023-02-06 14:50:27 UTC  
> Url: https://github.com/boostorg/url/issues/680  

When parsing URLs with invalid characters in the port number field, Boost.URL will erroneously prepend the invalid portion of the port into the path, instead of rejecting the URL.  
  
The relevant derivation rule portions from RFC3986:  
```  
   URI           = scheme ":" hier-part [ "?" query ] [ "#" fragment ]  
   hier-part     = "//" authority path-abempty  
   authority     = [ userinfo "@" ] host [ ":" port ]  
   port          = *DIGIT  
   path-abempty  = *( "/" segment )  
```  
  
Thus, an RFC-compliant parser should reject any URL in which the first character after a port number is neither `'/'`, `'?'`, `'#'`, nor the string terminator.  
  
The following short program demonstrates the bug:  
```C++  
#include <boost/url/src.hpp>  
#include <iostream>  
  
int main() {  
    boost::urls::url const u("http://example.com:80INVALID_JUNK/path");  
    std::cout << "Scheme:   " << (u.scheme() != std::string() ? u.scheme() : "(nil)") << "\n";  
    std::cout << "Host:     " << (u.host() != std::string() ? u.host() : "(nil)") << "\n";  
    std::cout << "Path:     " << (u.path() != std::string() ? u.path() : "(nil)") << "\n";  
    std::cout << "Port:     " << (u.port() != std::string() ? u.port() : "(nil)") << "\n";  
    std::cout << "Query:    " << (u.query() != std::string() ? u.query() : "(nil)") << "\n";  
    std::cout << "Username: " << (u.userinfo() != std::string() ? u.userinfo() : "(nil)") << "\n";  
    std::cout << "Fragment: " << (u.fragment() != std::string() ? u.fragment() : "(nil)") << "\n";  
}  
```  
  
Its output:  
```  
Scheme:   http  
Host:     example.com  
Path:     INVALID_JUNK/path  
Port:     80  
Query:    (nil)  
Username: (nil)  
Fragment: (nil)  
```  
  
Output of `g++ -Q -v`:  
```  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-pc-linux-gnu/12.2.1/lto-wrapper  
Target: x86_64-pc-linux-gnu  
Configured with: /build/gcc/src/gcc/configure --enable-languages=c,c++,ada,fortran,go,lto,objc,obj-c++,d --enable-bootstrap --prefix=/usr --libdir=/usr/lib --libexecdir=/usr/lib --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=https://bugs.archlinux.org/ --with-build-config=bootstrap-lto --with-linker-hash-style=gnu --with-system-zlib --enable-__cxa_atexit --enable-cet=auto --enable-checking=release --enable-clocale=gnu --enable-default-pie --enable-default-ssp --enable-gnu-indirect-function --enable-gnu-unique-object --enable-libstdcxx-backtrace --enable-link-serialization=1 --enable-linker-build-id --enable-lto --enable-multilib --enable-plugin --enable-shared --enable-threads=posix --disable-libssp --disable-libstdcxx-pch --disable-werror  
Thread model: posix  
Supported LTO compression algorithms: zlib zstd  
gcc version 12.2.1 20230111 (GCC)  
```  
  
Boost version 1.81.0.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-06 13:00:40 UTC  
> Url: https://github.com/boostorg/url/issues/680#issuecomment-1419042148  

I think this is already fixed for 1.82.0. Is there a chance you can try the tip of develop?

---

## Comment 2

> Username: kenballus  
> Created at: 2023-02-06 14:50:23 UTC  
> Url: https://github.com/boostorg/url/issues/680#issuecomment-1419205720  

Yep! You're right. Closing the issue.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-06 14:54:29 UTC  
> Url: https://github.com/boostorg/url/issues/680#issuecomment-1419212146  

\phew :)
