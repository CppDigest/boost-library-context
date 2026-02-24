# #134 - boost::interprocess::file_mapping does not support Unicode paths [Closed]

> Username: mikekaganski  
> Created at: 2020-12-28 09:23:35 UTC  
> Updated at: 2021-01-30 23:27:42 UTC  
> Closed at: 2021-01-30 23:27:42 UTC  
> Url: https://github.com/boostorg/interprocess/issues/134  

`boost::interprocess::file_mapping` currently only accepts a char* path in its constructor. On Windows, it makes it impossible to use the class (and anything using files) with files like `テストabcабв.ext` (the name deliberately uses Japanese, English and Cyrillic characters for testing; this would only work on currently beta UFT-8 codepage support in Windows 10).  
  
On the other hand, there is `boost::filesystem::path`, which documents *how it should be* - i.e., allowing both wchar_t* initialization paths, and also (yes, deprecated in C++17) std::wstring_convert passed to define the char-to-wchar_t conversion. Possibly, to minimize duplication, `boost::interprocess::file_mapping` could just allow passing a `boost::filesystem::path` to its constructor ...  
  
See also https://github.com/boostorg/interprocess/issues/93.
