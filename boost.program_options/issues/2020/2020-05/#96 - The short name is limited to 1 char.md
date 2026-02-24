# #96 - The short name is limited to 1 char [Open]

> Username: bcristian  
> Created at: 2020-05-07 16:39:33 UTC  
> Updated at: 2020-05-07 16:39:33 UTC  
> Url: https://github.com/boostorg/program_options/issues/96  

Version 1.73.0  
  
Documentation (https://www.boost.org/doc/libs/1_73_0/doc/html/boost/program_options/option_description.html#id-1_3_31_9_7_1_1_2_5-bb):  
  
> The 'name' parameter is interpreted by the following rules:  
> if there's no "," character in 'name', it specifies long name  
> otherwise, the part before "," specifies long name and the part after -- short name.  
  
Code, options_descriptions.cpp, line 205:  
```  
if (last_name.length() == 1) {  
                m_short_name = '-' + last_name;  
                m_long_names.pop_back();  
```  
  
This limitation is not mentioned in the docs.  
I would prefer that short names of several characters are allowed, but otherwise the limitations should be at least mentioned.  
Side note: the possibility of having several long names separated by colons is not mentioned there, either.
