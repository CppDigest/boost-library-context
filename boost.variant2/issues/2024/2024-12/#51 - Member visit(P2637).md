# #51 - Member visit(P2637) [Open]

> Username: libbooze  
> Created at: 2024-12-21 16:44:42 UTC  
> Updated at: 2024-12-24 15:31:56 UTC  
> Url: https://github.com/boostorg/variant2/issues/51  

[P2637](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p2637r3.html) is merged into C++26 [draft](https://eel.is/c++draft/variant.visit#9).   
  
Implementation should not be hard, although a bit more complicated than in `std::variant`since I presume we want to allow this functionality in standards that do not support deducing this.   
  
If maintainer is fine with this change I could try to implement it, as mentioned logic does not seem hard beside code duplication due to lack of deducing this.   
  
  
edit: if maintainer feels functionality for older standards is not worth the trouble then I believe at least we should implement version with deducing this in newer standards to keep variant2 API matching `std::variant`.
