# #243 - `find_include_file` incorrectly accepts directories as include files [Open]

> Username: Rac75116  
> Created at: 2025-11-10 10:32:07 UTC  
> Updated at: 2025-11-10 12:12:44 UTC  
> Url: https://github.com/boostorg/wave/issues/243  

https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/cpp_include_paths.hpp#L331  
https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/cpp_include_paths.hpp#L365  
  
`boost::wave::utils::include_paths::find_include_file` validates a candidate include path using `boost::filesystem::exists`. Because `exists` also returns true for directories, subsequent code that expects a file may attempt to open a directory and encounter a runtime error. Replacing `exists` with `boost::filesystem::is_regular_file` would ensure only regular files are accepted.

---

## Comment 1

> Username: hkaiser  
> Created at: 2025-11-10 12:12:44 UTC  
> Url: https://github.com/boostorg/wave/issues/243#issuecomment-3511250014  

Nice catch! Thanks!
