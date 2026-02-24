# #151 Move 'arg' variable to detail namespace (issue #150) [Merged]

> Username: Copilot  
> Created at: 2025-12-04 10:49:27 UTC  
> Updated at: 2025-12-04 11:07:35 UTC  
> Merged at: 2025-12-04 11:07:35 UTC  
> Closed at: 2025-12-04 11:07:35 UTC  
> Url: https://github.com/boostorg/program_options/pull/151  

The `arg` variable is an implementation detail used as the default placeholder text for option arguments in help messages. It was incorrectly exposed in the public `boost::program_options` namespace.  
  
## Changes  
  
- **include/boost/program_options/detail/value_semantic.hpp**: Moved extern declaration into `detail` namespace; updated reference in `typed_value<T, charT>::name()`  
- **src/value_semantic.cpp**: Moved definition into `detail` namespace; updated reference in `untyped_value::name()`  
  
Before:  
```cpp  
namespace boost { namespace program_options {  
    extern BOOST_PROGRAM_OPTIONS_DECL std::string arg;  
    // ...  
    std::string const& var = (m_value_name.empty() ? arg : m_value_name);  
}}  
```  
  
After:  
```cpp  
namespace boost { namespace program_options {  
    namespace detail {  
        extern BOOST_PROGRAM_OPTIONS_DECL std::string arg;  
    }  
    // ...  
    std::string const& var = (m_value_name.empty() ? detail::arg : m_value_name);  
}}  
```  
  
<!-- START COPILOT CODING AGENT SUFFIX -->  
  
  
  
<details>  
  
<summary>Original prompt</summary>  
  
> in the detail/value_semantic.hpp, move the 'arg' variable to 'detail' sub-namespace. Adjust value_semantic.cpp to match. In the PR, mention issue #150  
  
  
</details>  
  
  
  
<!-- START COPILOT CODING AGENT TIPS -->  
---  
  
💡 You can make Copilot smarter by setting up custom instructions, customizing its development environment and configuring Model Context Protocol (MCP) servers. Learn more [Copilot coding agent tips](https://gh.io/copilot-coding-agent-tips) in the docs.

---
