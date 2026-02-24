# #31 - exception thrown: std::out_of_range [Closed]

> Username: m0rl  
> Created at: 2017-07-21 15:21:52 UTC  
> Updated at: 2017-07-21 15:30:58 UTC  
> Closed at: 2017-07-21 15:30:58 UTC  
> Url: https://github.com/boostorg/program_options/issues/31  

`error_with_option_name::what` throws an exception of type `std::out_of_range` when  
`option` placeholder is set up but no (or empty) `original_token` is provided.  
  
`strip_prefixes` does not validate `text` argument  
  
```c++  
inline std::string strip_prefixes(const std::string& text)  
{  
    // "--foo-bar" -> "foo-bar"  
    return text.substr(text.find_first_not_of("-/"));  
}  
```  
  
and `get_canonical_option_name` passes to it whatever end user has provided to  
the `error_with_option_name` ctor as an `original_token` argument  
  
```c++  
error_with_option_name::error_with_option_name(...,   
                                               const std::string& original_token,  
                                               ...)  
{  
  m_substitutions["original_token"] = original_token;  
}  
  
string error_with_option_name::get_canonical_option_name() const  
{  
  if (!m_substitutions.find("option")->second.length())  
      return m_substitutions.find("original_token")->second;  
  
  string original_token = strip_prefixes(m_substitutions.find("original_token")->second);  
  string option_name = strip_prefixes(m_substitutions.find("option")->second);  
```  
tested with boost `1.64.0`
