# #121 Customization point for to-string conversion [Open]

> Username: ecatmur  
> Created at: 2023-02-22 18:46:36 UTC  
> Updated at: 2023-02-23 00:45:23 UTC  
> Url: https://github.com/boostorg/program_options/pull/121  

The use of boost::lexical_cast for default_value and implicit_value has several deficiencies; firstly, with the adoption of std::format in C++20 it is becoming more and more usual for libraries to regard iostreams formatting as deprecated and not provide ostream operator<< overloads for classes. Secondly, the standard library has added ostreams formatting for classes (esp. std::chrono) at different times, making it confusing when code works on one compiler and not another; it is possible to always use the two-argument overloads of default_value and implicit_value, but this is extra repeated work for the user and encourages providing string literals which may get out of sync with the actually provided default/implicit value. Finally, as a general point, when validate() is customized it may use a different format than iostreams (often terser and avoiding characters that are difficult to supply on the command line).  
  
In conclusion, it would be useful to lexical_cast to std::string indirectly, via a customization point that can be customized by the user specifically for Boost.ProgramOptions.  
  
This PR supplies a customization point in boost::program_options namespace with primary overload:  
  
    template<class T>  
    std::string make_textual(const T& v, value_semantic*, long)  
    {  
        return boost::lexical_cast<std::string>(v);  
    }  
  
The first parameter is the value being textualized (by default_value or implicit_value single-argument overloads). The second parameter is mainly for ADL and disambiguation, but also if non-null it provides access to the 'value_semantic' being modified. The third parameter is for overload resolution, as with 'validate'.  
  
There is one overload provided, for std::vector<T>:  
  
    template<class T>  
    std::string make_textual(const std::vector<T>& v, value_semantic*, long)  
    {  
        std::string textual;  
        for (unsigned i = 0; i < v.size(); ++i)  
        {  
            if (i != 0)  
                textual += ' ';  
            textual += make_textual(v[i], (value_semantic*)0, 0);  
        }  
        return textual;  
    }  
  
This mirrors the overload of 'validate' for std::vector<T>, since it would be surprising for 'make_textual' to work where 'validate' does. It also illustrates how to recursively invoke 'make_textual', and that the 'value_semantic' parameter may be null. This is just conceivably a breaking change if some user has provided operator<< for std::vector<T> but not for T for some type T, but this seems very unlikely.

---
