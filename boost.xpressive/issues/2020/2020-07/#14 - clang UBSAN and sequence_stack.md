# #14 - clang UBSAN and sequence_stack [Closed]

> Username: alextibbles  
> Created at: 2020-07-12 20:33:26 UTC  
> Updated at: 2020-07-12 20:53:17 UTC  
> Closed at: 2020-07-12 20:53:16 UTC  
> Url: https://github.com/boostorg/xpressive/issues/14  

With clang-10 default set of UBSAN checks, the following gets triggered:  
`/usr/include/boost/xpressive/detail/utility/sequence_stack.hpp:217:21: runtime error: applying non-zero offset 4 to null pointer`  
```  
#include <boost/xpressive/detail/utility/sequence_stack.hpp>  
  
int main() {  
	boost::xpressive::detail::sequence_stack<int> s;  
	s.push_sequence(1, 42);  
	return 0;  
}  
```  
  
The following patch avoids the sanitizer error:  
```  
216,220c216,217  
<         // Advance the high-water mark  
<         this->curr_ += count;  
<   
<         // Check to see if we have overflowed this buffer  
<         if(std::less<void*>()(this->end_, this->curr_))  
---  
>         // Check to see if we would overflow this buffer  
>         if((nullptr == ptr) || (std::less<void*>()(this->end_, ptr + count)))  
222,224d218  
<             // oops, back this out.  
<             this->curr_ = ptr;  
<   
227a222,224  
>           
>         // Advance the high-water mark  
>         this->curr_ += count;  
```  
Apologies, I couldn't find where the CI output for boost.org is published to see if this shows up in existing tests.  
Reproducer attached:  
[sequence_stack_ubsan.zip](https://github.com/boostorg/xpressive/files/4909417/sequence_stack_ubsan.zip)

---

## Comment 1

> Username: alextibbles  
> Created at: 2020-07-12 20:53:16 UTC  
> Url: https://github.com/boostorg/xpressive/issues/14#issuecomment-657273615  

Looks like this is already fixed in develop.
