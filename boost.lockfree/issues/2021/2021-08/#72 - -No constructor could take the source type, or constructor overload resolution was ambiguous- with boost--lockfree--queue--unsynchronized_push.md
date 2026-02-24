# #72 - "No constructor could take the source type, or constructor overload resolution was ambiguous" with boost::lockfree::queue::unsynchronized_push [Open]

> Username: HuangRuixiao-eng  
> Created at: 2021-08-27 13:32:07 UTC  
> Updated at: 2021-11-02 12:34:36 UTC  
> Url: https://github.com/boostorg/lockfree/issues/72  

When I used the code below in vs 2019:  
```  
#include<boost/lockfree/queue.hpp>  
  
int main() {  
  boost::lockfree::queue<unsigned, boost::lockfree::capacity<32>> q;  
  q.unsynchronized_push(1);  
  return 0;  
}  
```  
  
  
The building failed and reported:  
> 1>D:\boost_1_77_0\boost\lockfree\queue.hpp(377,45): error C2039: 'next': is not a member of 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\detail\freelist.hpp(269): message : see declaration of 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(378,36): error C2039: 'get_ptr': is not a member of 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\detail\freelist.hpp(269): message : see declaration of 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(381,23): error C2039: 'next': is not a member of 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\detail\freelist.hpp(269): message : see declaration of 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(381,52): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(369,1): message : No constructor could take the source type, or constructor overload resolution was ambiguous  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(382,47): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(369,1): message : No constructor could take the source type, or constructor overload resolution was ambiguous  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(386,47): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::lockfree::detail::tagged_index'  
1>D:\boost_1_77_0\boost\lockfree\queue.hpp(369,1): message : No constructor could take the source type, or constructor overload resolution was ambiguous
