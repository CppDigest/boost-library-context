# #128 string_path - std::basic_string_view enhancements [Open]

> Username: mknaleczb  
> Created at: 2025-09-09 08:46:49 UTC  
> Updated at: 2025-09-09 08:46:49 UTC  
> Url: https://github.com/boostorg/property_tree/pull/128  

In case of using C++17 standard or higher:  
1. string_path<> class gets new typedef strview_type = std::basic_string_view<char_type>;  
2. string_path<>::string_path(const String&, ...) is replaced by string_path<>::string_path(strview_type, ...);  
3. dump_sequence(...) functions are modified from const {std::string/std::wstring}& to {std::string_view/std::wstring_view}  
  
Reasoning:   
During using boost::property_tree::wptree with standard C++20 I needed to do some special actions when using std::wstring_view due to std::basic_string(std::basic_string_view) ctor being explicit:  
  
**_std::wstring_view pTreePath{L"some.example"};  
wpath rootPTreePath = wpath(L"root") / std::wstring(pTreePath);  
wptree pTree{};  
...  
const auto val = pTree.get_optional<int>(pTreePath.data());_**  
  
----------------------  
For all standards:  
1. Added string_path<>::to_str() to expose string_path<>.m_value   
  
Reasoning:  
As someone works with wpath and with std::wstring, string_path<>::dump() forces conversion at first from (String == std::wstring) --(dump())-> std::string --(some other method)-> std::wstring

---
