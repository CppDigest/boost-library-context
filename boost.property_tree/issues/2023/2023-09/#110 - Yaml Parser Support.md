# #110 - Yaml Parser Support [Open]

> Username: sai-koushik-macha  
> Created at: 2023-09-20 00:15:30 UTC  
> Updated at: 2025-02-08 23:50:41 UTC  
> Url: https://github.com/boostorg/property_tree/issues/110  

So, will there be support for the yaml parser just like json parser. The requirement I am using json parser for reading purpose by reading json file and after reading I think it interally converts it into ptree. So, is it possible to do same with yaml

---

## Comment 1

> Username: ljluestc  
> Created at: 2025-02-08 23:50:39 UTC  
> Url: https://github.com/boostorg/property_tree/issues/110#issuecomment-2645986311  

```  
#include <boost/property_tree/ptree.hpp>  
#include <boost/property_tree/yaml_parser.hpp>  
#include <iostream>  
#include <fstream>  
  
int main() {  
    try {  
        // Create a property tree  
        boost::property_tree::ptree pt;  
  
        // Read YAML from a file  
        std::ifstream yaml_input("input.yaml");  
        if (!yaml_input) {  
            throw std::runtime_error("Failed to open input.yaml");  
        }  
        boost::property_tree::read_yaml(yaml_input, pt);  
  
        // Access and print data from the YAML file  
        std::cout << "Name: " << pt.get<std::string>("name") << std::endl;  
        std::cout << "Age: " << pt.get<int>("age") << std::endl;  
        std::cout << "Address: " << pt.get<std::string>("address.city") << ", " << pt.get<std::string>("address.state") << std::endl;  
  
        // Modify the property tree  
        pt.put("age", 31); // Update age  
        pt.put("address.zip", "12345"); // Add a new field  
  
        // Write the modified property tree to a new YAML file  
        std::ofstream yaml_output("output.yaml");  
        if (!yaml_output) {  
            throw std::runtime_error("Failed to open output.yaml");  
        }  
        boost::property_tree::write_yaml(yaml_output, pt);  
  
        std::cout << "Modified YAML written to output.yaml" << std::endl;  
    } catch (const std::exception& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
    }  
  
    return 0;  
}  
  
```
