# #266 - file_mapping should be able to take a boost::filesystem or std::filesystem Path object in constructor [Closed]

> Username: jlmxyz  
> Created at: 2025-06-18 19:27:52 UTC  
> Updated at: 2026-01-07 10:00:23 UTC  
> Closed at: 2026-01-07 10:00:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/266  

### Version of Boost  
  
1.88  
  
### Actual and Expected Behavior  
<pre>  
namespace bi = boost::interprocess;  
namespace fs = boost::filesystem;  
if (fs::is_regular_file (x.path ())  
                   && x.path ().extension () == ".json")  
            {  
              cout << "processing " << x.path () << std::endl;  
              // create memory mapped object  
              bi::file_mapping lJsonFile (x.path (), bi::read_only);   //error, passing a path instead of const char*  
              std::size_t lFileSize = x.path ().size ();  
              bi::mapped_region lFileMapp(lJsonFile, bi::read_only);  
            }  
</pre>  
a workaround can be done with   
  
`  
bi::file_mapping lJsonFile (x.path ().c_str(), bi::read_only);   
`  
  
however filesystems names can not be "translated" as c string : today filesystems are mostly unicode, if utf_8 filesystem are c_str compatible, it's not the case when utf_16, utf_32.... at least the path should be a std::u8string_view that will cause utf convertion  
  
char* file name are now quite confusing, std::filesystem/boost::filesystem solve/clarify the issue of unicode path as u8string(_view) u16string(_view) and u32string(_view) does clarify code

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 10:00:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/266#issuecomment-3718114995  

There is no plan to add support for filesystem paths, sorry. The char overloads are modeled as the char overloads of the operating system calls. Boost.Interprocess does whatever the OS does with char paths, so that the user has control over what's happening and how to change it (locales, utf-8 support, etc.)
