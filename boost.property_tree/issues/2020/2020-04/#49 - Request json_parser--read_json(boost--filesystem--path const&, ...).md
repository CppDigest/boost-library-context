# #49 - Request json_parser::read_json(boost::filesystem::path const&, ...) [Open]

> Username: mfsv  
> Created at: 2020-04-28 18:41:55 UTC  
> Updated at: 2025-02-08 23:42:09 UTC  
> Url: https://github.com/boostorg/property_tree/issues/49  

There are currently two signatures available:  
- `read_json(std::string const&, ...)`  
- `read_json(std::basic_istream<...> const&, ...)`  
  
In case of Windows and a file name with non-standard characters I cannot rely on `read_json(std::string const&, ...)`.  
  
I therefore use this workaround:  
`boost::filesystem::path const filename(L"Some foreign language file name");`  
`boost::filesystem::ifstream ifs(filename);`  
`read_json(ifs, ...);`  
  
In case of an error, the exception thrown does not contain the filename and I have to construct a message of my own.  
  
I would like to request these additions:  
- `read_json(boost::filesystem::path const&, ...)`  
- `read_json(std::wstring const&, ...)`

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-08 23:42:07 UTC  
> Url: https://github.com/boostorg/property_tree/issues/49#issuecomment-2645984356  

```  
  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/json_parser.hpp>  
#include <boost/filesystem/path.hpp>  
#include <iostream>  
  
int main() {  
    try {  
        boost::property_tree::ptree pt;  
  
        // Read JSON using boost::filesystem::path  
        boost::filesystem::path path(L"Some foreign language file name.json");  
        boost::property_tree::json_parser::read_json(path, pt);  
  
        // Read JSON using std::wstring  
        std::wstring wfilename = L"Another foreign language file name.json";  
        boost::property_tree::json_parser::read_json(wfilename, pt);  
  
        // Access and print the JSON data  
        std::cout << "Root element: " << pt.get<std::string>("root") << std::endl;  
    } catch (const std::exception& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
    }  
  
    return 0;  
}  
```
