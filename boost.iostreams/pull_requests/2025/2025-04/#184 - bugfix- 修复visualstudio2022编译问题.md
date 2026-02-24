# #184 bugfix: 修复visualstudio2022编译问题 [Open]

> Username: ulinzeng  
> Created at: 2025-04-25 08:44:48 UTC  
> Updated at: 2025-04-25 08:44:48 UTC  
> Url: https://github.com/boostorg/iostreams/pull/184  

在新的C++标准中允许std::streampos直接转换成std::fpos_t，并且在visual studio2022中seekpos的定义被删除，导致编译失败。

---
