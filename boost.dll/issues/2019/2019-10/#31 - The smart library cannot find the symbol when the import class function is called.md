# #31 - The smart library cannot find the symbol when the import class function is called [Closed]

> Username: XiaLiChao82  
> Created at: 2019-10-07 14:27:15 UTC  
> Updated at: 2019-10-08 11:46:44 UTC  
> Closed at: 2019-10-08 11:46:44 UTC  
> Url: https://github.com/boostorg/dll/issues/31  

using boost::typeindex::ctti_type_index;  
using boost::typeindex::stl_type_index;  
cout << "type name:" << stl_type_index::type_id<const char*>() << endl;//print "char const*"  
cout << "type name:" << ctti_type_index::type_id<const char*>() << endl;//print "const char*  
  
//mangled_storage_base.hpp output "char const*"  
void add_symbols(const std::vector<std::string> & symbols) //demangle symbol is "char const *"  
  
//demangle symbol  
inline char const * demangle_alloc( char const * name ) BOOST_NOEXCEPT  
{  
    int status = 0;  
    std::size_t size = 0;  
    return abi::__cxa_demangle( name, NULL, &size, &status );  
}  
  
//mangled_storage_base.hpp demangled symbol is "const char*"  
std::string get_name() const  
    {  
        using boost::typeindex::ctti_type_index;  
        auto tx = ctti_type_index::type_id<T>();  
        auto val = (aliases_.count(tx) > 0) ? aliases_.at(tx) : tx.pretty_name();  
        return val;  
    }  
  
ctti_type_index and stl_type_index export characters are inconsistent, how to use typeid() of ctti_type_index to determine whether typeid() of stl_type_index exists?
