# #134 - Add Transparent Comparator Support [Open]

> Username: joesdiner  
> Created at: 2024-04-19 13:37:00 UTC  
> Updated at: 2024-04-21 19:09:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/134  

It would be great of `boost::program_options::variables_map` could have [transparent comparator](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3465.pdf) support (c++14 feature), to potentially avoid unnecessary object creation when accessing values in the map. Theoretically only a `std::less<>` needs to be added as the third template parameter of the `std::map` `variables_map` inherits from. Currently:  
  
````c++  
// 1  
boost::program_options::variable_map vm;  
vm.find("test"); // implicit std::string object created and destoryed   
  
// 2  
std::string_view sv("test");  
vm.find(sv); // does not compile  
  
// 3  
vm.find(std::string(sv)); // only way to inspect with a std::string_view, explicit std::string object created and destroyed  
````  
With a transparent comparator 1 will not create a `std::string` object and just compare directly against the c-string literal. 2 will work as shown without any extra objects being created. 3 will be unnecessary
