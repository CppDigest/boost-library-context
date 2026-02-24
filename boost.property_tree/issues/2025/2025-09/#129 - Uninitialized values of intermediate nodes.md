# #129 - Uninitialized values of intermediate nodes [Open]

> Username: ilya-talankin  
> Created at: 2025-09-30 06:09:09 UTC  
> Updated at: 2025-09-30 06:09:09 UTC  
> Url: https://github.com/boostorg/property_tree/issues/129  

If you add values along a multi-level path to the ptree, the values of the intermediate nodes remain uninitialized. The sample code is below  
```cpp  
#include <boost/property_tree/ptree.hpp>  
#include <iostream>  
  
enum class NodeType  
{  
    Intermediate,  
    Root,  
    Leaf  
};  
  
using Tree = boost::property_tree::basic_ptree<std::string, NodeType>;  
  
void Print(const Tree& tree, std::string_view name)  
{  
    const auto value = tree.get_value(NodeType::Intermediate);  
    std::cout << "Name: " << name << ", value: " << static_cast<int>(value) << std::endl;  
    for (const auto& [childName, subtree]: tree)  
        Print(subtree, childName);  
}  
  
int main() {  
    Tree tree;  
    tree.add("Intermediate1.Intermediate2.Leaf", NodeType::Leaf);  
    tree.put_value(NodeType::Root);  
    Print(tree, "Root");  
}  
```  
Compiled with boost 1.87 by x86-64 clang-18.1.0  
[Godbolt link](https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:69,endLineNumber:16,positionColumn:69,positionLineNumber:16,selectionStartColumn:69,selectionStartLineNumber:16,startColumn:69,startLineNumber:16),source:'%23include+%3Cboost/property_tree/ptree.hpp%3E%0A%23include+%3Ciostream%3E%0A%0Aenum+class+NodeType%0A%7B%0A++++Intermediate,%0A++++Root,%0A++++Leaf%0A%7D%3B%0A%0Ausing+Tree+%3D+boost::property_tree::basic_ptree%3Cstd::string,+NodeType%3E%3B%0A%0Avoid+Print(const+Tree%26+tree,+std::string_view+name)%0A%7B%0A++++const+auto+value+%3D+tree.get_value(NodeType::Intermediate)%3B%0A++++std::cout+%3C%3C+%22Name:+%22+%3C%3C+name+%3C%3C+%22,+value:+%22+%3C%3C+static_cast%3Cint%3E(value)+%3C%3C+std::endl%3B%0A++++for+(const+auto%26+%5BchildName,+subtree%5D:+tree)%0A++++++++Print(subtree,+childName)%3B%0A%7D%0A%0Aint+main()+%7B%0A++++Tree+tree%3B%0A++++tree.add(%22Intermediate1.Intermediate2.Leaf%22,+NodeType::Leaf)%3B%0A++++tree.put_value(NodeType::Root)%3B%0A++++Print(tree,+%22Root%22)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.29692832764505,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:clang1810,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!((name:boost,ver:'187'),(name:fmt,ver:'713')),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+clang+18.1.0+(C%2B%2B,+Editor+%231)',t:'0')),header:(),k:38.70307167235495,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4)  
Possible output:  
```  
Name: Root, value: 1  
Name: Intermediate1, value: 0  
Name: Intermediate2, value: -249048016  
Name: Leaf, value: 2  
```  
One of the correction options https://github.com/ilya-talankin/property_tree/pull/1/files
