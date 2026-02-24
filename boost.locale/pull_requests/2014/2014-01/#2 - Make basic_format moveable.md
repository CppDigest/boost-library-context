# #2 Make basic_format moveable [Closed]

> Username: zauguin  
> Created at: 2014-01-23 21:53:45 UTC  
> Updated at: 2022-10-17 08:27:12 UTC  
> Closed at: 2022-10-17 08:27:12 UTC  
> Url: https://github.com/boostorg/locale/pull/2  

Currently basic_format can't be returned from a function, which can be fixed with a default move constructor.

---

## Comment 1

> Username: mvyskoc  
> Created_at: 2016-03-06 10:12:09 UTC  
> Url: https://github.com/boostorg/locale/pull/2#issuecomment-192866359  

Hello,  
I found missing enconding `cp852` in the file `locale\src\encoding\wconv_codepage.ipp`. I fix it by adding this encoding to the following table. Everything is working now. I use Czech Windows XP and MSVC Express 2010. Czech Windows use codepage 852 in text terminal `cmd`  and codepage 1250 in other windows applications. Please, please repair. I think for the Czech console program is very important.  Thank you Martin  
  
```  
windows_encoding all_windows_encodings[] = {  
        { "big5",       950, 0 },  
        { "cp1250",     1250, 0 },  
        { "cp1251",     1251, 0 },  
        { "cp1252",     1252, 0 },  
        { "cp1253",     1253, 0 },  
        { "cp1254",     1254, 0 },  
        { "cp1255",     1255, 0 },  
        { "cp1256",     1256, 0 },  
        { "cp1257",     1257, 0 },  
        { "cp852",      852, 0 },  
        { "cp874",      874, 0 },  
        { "cp932",      932, 0 },  
        { "cp936",      936, 0 },  
        { "eucjp",      20932, 0 },  
        { "euckr",      51949, 0 },  
        { "gb18030",    54936, 0 },  
        { "gb2312",     20936, 0 },  
        { "gbk",        936, 0 },  
        { "iso2022jp",  50220, 0 },  
        { "iso2022kr",  50225, 0 },  
        { "iso88591",   28591, 0 },  
        { "iso885913",  28603, 0 },  
        { "iso885915",  28605, 0 },  
        { "iso88592",   28592, 0 },  
        { "iso88593",   28593, 0 },  
        { "iso88594",   28594, 0 },  
        { "iso88595",   28595, 0 },  
        { "iso88596",   28596, 0 },  
        { "iso88597",   28597, 0 },  
        { "iso88598",   28598, 0 },  
        { "iso88599",   28599, 0 },  
        { "koi8r",      20866, 0 },  
        { "koi8u",      21866, 0 },  
        { "ms936",      936, 0 },  
        { "shiftjis",   932, 0 },  
        { "sjis",       932, 0 },  
        { "usascii",    20127, 0 },  
        { "utf8",       65001, 0 },  
        { "windows1250",        1250, 0 },  
        { "windows1251",        1251, 0 },  
        { "windows1252",        1252, 0 },  
        { "windows1253",        1253, 0 },  
        { "windows1254",        1254, 0 },  
        { "windows1255",        1255, 0 },  
        { "windows1256",        1256, 0 },  
        { "windows1257",        1257, 0 },  
        { "windows852",         852, 0 },  
        { "windows874",         874, 0 },  
        { "windows932",         932, 0 },  
        { "windows936",         936, 0 },  
    };  
```

---
