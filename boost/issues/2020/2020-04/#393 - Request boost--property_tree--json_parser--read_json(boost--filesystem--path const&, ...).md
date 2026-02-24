# #393 - Request boost::property_tree::json_parser::read_json(boost::filesystem::path const&, ...) [Closed]

> Username: mfsv  
> Created at: 2020-04-28 18:35:46 UTC  
> Updated at: 2020-04-28 18:39:36 UTC  
> Closed at: 2020-04-28 18:39:36 UTC  
> Url: https://github.com/boostorg/boost/issues/393  

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
