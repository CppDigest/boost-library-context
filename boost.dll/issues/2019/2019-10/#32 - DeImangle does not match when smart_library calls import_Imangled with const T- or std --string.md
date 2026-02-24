# #32 - DeImangle does not match when smart_library calls import_Imangled with const T* or std ::string [Closed]

> Username: XiaLiChao82  
> Created at: 2019-10-22 07:23:37 UTC  
> Updated at: 2019-11-27 18:11:49 UTC  
> Closed at: 2019-11-27 18:11:49 UTC  
> Url: https://github.com/boostorg/dll/issues/32  

DeImangle does not match when smart_library calls import_Imangled with const T* or std ::string.I solved this problem by modifying the type_rule function of itanium.h file. The modified code is as follows:  
      
template<typename T>  
    std::string type_rule(const std::string & type_name)  
    {  
        using namespace std;  
  
//-        return  type_name +  
// -               const_rule<T>() +  
// -               volatile_rule<T>() +  
// -               reference_rule<T>();  
    +    (void)type_name;  
    +   return boost::core::demangle(typeid (T).name()) + const_rule<T>() + volatile_rule<T>() + reference_rule<T>();  
    }

---

## Comment 1

> Username: RamilGauss  
> Created at: 2019-11-09 15:29:05 UTC  
> Updated at: 2019-11-09 16:29:56 UTC  
> Url: https://github.com/boostorg/dll/issues/32#issuecomment-552110315  

I met this bug too.  
For example (for Linux only):  
`  
class MyClass  
{  
public:  
  template <typename T>  
  int Func(){return 42;}  
};  
import_mangled<MyClass, int( void )>( lib, "Func<float>"  ); // throw exception!!!  
`  
Because: in Linux export table contains `int MyClass::Func<float>()`, but expected in import_mangled `MyClass::Func<float>()` without returned type.  
  
  
itanium.hpp:  
`  
template<typename Class, typename Func>  
std::string mangled_storage_impl::get_mem_fn(const std::string &name) const  
`  
maybe:   
`auto found = std::find_if(storage_.begin(), storage_.end(), [&](const entry& e) {return e.demangled.find( matcher ) != std::string::npos;});`

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-11-09 18:27:32 UTC  
> Url: https://github.com/boostorg/dll/issues/32#issuecomment-552125070  

I'm out of time for the next X months, so this issue would not be solved soon. But if you provide a Pull Request with test case and a fix, then I'll be happy to merge it.

---

## Comment 3

> Username: RamilGauss  
> Created at: 2019-11-10 13:29:56 UTC  
> Url: https://github.com/boostorg/dll/issues/32#issuecomment-552194980  

Ok.

---

## Comment 4

> Username: XiaLiChao82  
> Created at: 2019-11-11 05:34:33 UTC  
> Url: https://github.com/boostorg/dll/issues/32#issuecomment-552299823  

The problem I encountered was that the CV qualifier string order of the pointer type in mangled exported under Linux was inconsistent with demangled, causing an exception to be thrown, such as const char* (ctti::type_index: const char*; demangled: char const*), same for template type.

---

## Comment 5

> Username: RamilGauss  
> Created at: 2019-11-11 18:51:07 UTC  
> Url: https://github.com/boostorg/dll/issues/32#issuecomment-552566627  

Hello, XiaLiChao82.   
Can you give an example of code in which there will definitely be an exception?

---

## Comment 6

> Username: XiaLiChao82  
> Created at: 2019-11-12 01:13:49 UTC  
> Updated at: 2019-11-12 05:39:21 UTC  
> Url: https://github.com/boostorg/dll/issues/32#issuecomment-552688679  

> Hello, XiaLiChao82.  
> Can you give an example of code in which there will definitely be an exception?  
[linux]  
#test.h  
```  
namespace space {  
  
class my_plugin_api {  
 public:  
  virtual const char* name() const = 0;  
  
  virtual ~my_plugin_api() {}  
};  
class BOOST_SYMBOL_EXPORT my_plugin : public my_plugin_api {  
  std::string _name;  
  std::function<void(const char*, const char*, const char*)> callback;  
  
 public:  
  const char* name() const;  
  float calculate(float x, float y);  
  int calculate(const int x, const int y);  
  void set_name(char* name);  
  void set_name(const std::string& name);  
  void set_callback(  
      const std::function<void(const char*, const char*, const char*)>&,  
      const char*,  
      const char*,  
      const char*);  
  static std::size_t size();  
  my_plugin();  
  my_plugin(const char*);  
  ~my_plugin();  
  static int value;  
};  
}  
  
```  
#test.cpp  
```  
#include "test.h"  
const char* space::my_plugin::name() const {  
  return _name.c_str();  
}  
float space::my_plugin::calculate(float x, float y) {  
  return x / y;  
}  
int space::my_plugin::calculate(const int x, const int y) {  
  return x / y;  
}  
void space::my_plugin::set_name(char* name) {  
  _name = name;  
}  
void space::my_plugin::set_name(const std::string& name) {  
  _name = name;  
}  
void space::my_plugin::set_callback(  
    const std::function<void(const char*, const char*, const char*)>& _cb,  
    const char* c1,  
    const char* c2,  
    const char* c3) {  
  callback = _cb;  
  callback(c1, c2, c3);  
}  
std::size_t space::my_plugin::size() {  
  return sizeof(my_plugin);  
}  
space::my_plugin::my_plugin(const char* name) : _name(name) {}  
space::my_plugin::my_plugin() : _name("Empty") {}  
space::my_plugin::~my_plugin() {}  
int space::my_plugin::value = 42;  
```  
#main.cpp  
```  
#include <boost/dll/import_class.hpp>  
#include <boost/dll/import_mangled.hpp>  
#include <boost/dll/smart_library.hpp>  // for import_alias  
#include <boost/filesystem.hpp>  
#include <boost/type_index.hpp>  
#include <boost/type_index/ctti_type_index.hpp>  
#include <boost/type_index/stl_type_index.hpp>  
  
namespace dll = boost::dll;  
using namespace dll::experimental;  
using namespace std;  
  
int main(int argc, char* argv[]) {  
boost::dll::fs::path lib_path("test.dll");  // argv[1] contains path to directory  
                    // with our plugin library  
dll::experimental::smart_library lib(lib_path);  
auto cl = import_class<class Alias, const char*>(lib, plugin_name, size, "_name");  
//demangled: space::my_plugin(char const*)  
//real results: space::my_plugin(const char*)  
//Note the location of the CV qualifier  
  
  cout << "imported class" << endl;  
  
  auto callback = cl.call<void(  
      const std::function<void(const char *, const char *, const char *)> &,  
      const char *, const char *, const char *)>("set_callback");  
  
//demangled: space::my_plugin::set_callback(std::function<void (char const*, char const*, char const*)>&)  
//real results: space::my_plugin::set_callback(std::function<void(const char*, const char*, const char*)>&)  
//'std::function<void (...)>: Note that 'void' and '(' have Spaces between them, which also causes string mismatches  
  callback(&printStr, "A", "B", "C");  
  
  auto name = import_mangled<const Alias, const char *()>(lib, "name");  
  std::cout << "Name: " << (cl->*name)() << std::endl;  
  //    (cl->*name)();  
  // import both calculate functions  
  auto calc =  
      import_mangled<Alias, float(float, float), int(const int, const int)>(  
          lib, "calculate");  
  std::cout << "Calc(float): " << (cl->*calc)(5.f, 2.f) << std::endl;  
  std::cout << "Calc(int): " << (cl->*calc)(5, 2) << std::endl;  
  auto setname = import_mangled<Alias, void(const std::string &)>(lib, "set_name");  
  char newName[] = "newName";  
  std::string stringNewName = "stringName";  
  (cl->*setname)(stringNewName);  
  std::cout << "new Name: " << (cl->*name)() << std::endl;  
  cout << "name:" << (t->*get_name)() << endl;  
  return 0;  
}  
```

---

## Comment 7

> Username: apolukhin  
> Created at: 2019-11-27 18:11:49 UTC  
> Url: https://github.com/boostorg/dll/issues/32#issuecomment-559197717  

Fixed in #35
