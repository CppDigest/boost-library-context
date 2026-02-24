# #78 - Lockfree Queue Triggers Thread Sanitizer Data Race Warning [Open]

> Username: chenhao94  
> Created at: 2022-06-07 23:28:48 UTC  
> Updated at: 2022-08-05 07:27:00 UTC  
> Url: https://github.com/boostorg/lockfree/issues/78  

If there are multiple consumers, there will be tsan data race warnings. Here is the code to reproduce:  
  
```  
>  cat lockfree_race.cc; echo ==================; clang++-12 lockfree_race.cc -std=c++20 -fsanitize="thread" -o lockfree_race; ./lockfree_race                                                                                                                                                                                         
#include <boost/version.hpp>                                                                                                                                                                                                                                                                                                                           
#include <boost/lockfree/queue.hpp>                                                                                                                                                                                                                                                                                                                    
                                                                                                                                                                            
#include <iostream>                                                                    
#include <thread>                                                                                                                                                           
                                                                                                                                                                                                                                                                                                                                                       
int main() {                                                                                                                                                                                                                                                                                                                                           
    std::cout << BOOST_VERSION << std::endl;                                                                                                                                                                                                                                                                                                           
    const int count = 1000000;                                                                                                                                                                                                                                                                                                                         
    boost::lockfree::queue<int> queue(10);                                                                                                                                                                                                                                                                                                             
    auto producer = [&queue](){                                                                                                                                                                                                                                                                                                                        
        for (int i = 0; i < count; ++i) {                                                                                                                                   
            queue.push(i);                             
        }                                                                                                                                                                   
    };                                                                                                                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                                                                                                       
    auto consumer = [&queue](){                                                                                                                                                                                                                                                                                                                        
        for (int i = 0; i < count; ++i) {                                                                                                                                   
            int val;                               
            queue.pop(val);                                                            
        }                                                                                                                                                                                                                                                                                                                                              
    };                                                                                                                                                                      
                                                                                                                                                                                                                                                                                                                                                       
    std::vector<std::thread> t;                                                                                                                                                                                                                                                                                                                        
    t.emplace_back(producer);                                                                                                                                                                                                                                                                                                                          
    t.emplace_back(consumer);                                                                                                                                                                                                                                                                                                                          
    t.emplace_back(consumer);                                                                                                                                               
                                                                                                                                                                            
    for (auto& tt : t) {                                                                                                                                                    
        tt.join();                                                                                                                                                                                                                                                                                                                                     
    }                                                                                                                                                                       
    return 0;                                   
}                                                                                      
==================                                                
107100                                         
==================                                                                                                                                                                                                                                                                                                                         [127/1026]  
WARNING: ThreadSanitizer: data race (pid=682920)                                                                                                                                                                                                                                                                                                       
  Write of size 8 at 0x7b10000087c0 by thread T2:                                                                                                                           
    #0 boost::lockfree::detail::tagged_ptr<boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::freelist_node>::set_ptr(boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::freelist_node*) <n  
ull> (lockfree_race+0x4bc1b3)                                                                                                                                               
    #1 boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::deallocate_impl(boost::lockfree::queue<int>::node*) <null> (lockfree_race+0x4c0cc6)  
    #2 void boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::deallocate<true>(boost::lockfree::queue<int>::node*) <null> (lockfree_race+0x4c0bd5)  
    #3 void boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::destruct<true>(boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> const&) <null> (lockfree_race+0x4c0b7a)  
    #4 bool boost::lockfree::queue<int>::pop<int>(int&) <null> (lockfree_race+0x4c0aba)                                    
    #5 boost::lockfree::queue<int>::pop(int&) <null> (lockfree_race+0x4c07c5)                                                                                               
    #6 main::$_1::operator()() const <null> (lockfree_race+0x4bb021)  
    #7 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) <null> (lockfree_race+0x4baf9d)  
    #8 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) <null> (lockfree_race+0x4baeed)  
    #9 void std::thread::_Invoker<std::tuple<main::$_1> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4bae95)         
    #10 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() <null> (lockfree_race+0x4bae35)                                                                                                                                                                                                                                                    
    #11 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() <null> (lockfree_race+0x4bad29)                                                                                                                                                                                                                             
    #12 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                            
  Previous atomic read of size 8 at 0x7b10000087c0 by thread T3:                                                                                                            
    #0 __tsan_atomic64_load <null> (lockfree_race+0x4717ce)                                                                                                                 
    #1 std::atomic<boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> >::load(std::memory_order) const <null> (lockfree_race+0x4bd37d)  
    #2 bool boost::lockfree::queue<int>::pop<int>(int&) <null> (lockfree_race+0x4c089b)                             
    #3 boost::lockfree::queue<int>::pop(int&) <null> (lockfree_race+0x4c07c5)                                                                                               
    #4 main::$_1::operator()() const <null> (lockfree_race+0x4bb021)                                                                                                        
    #5 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) <null> (lockfree_race+0x4baf9d)                                                           
    #6 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) <null> (lockfree_race+0x4baeed)  
    #7 void std::thread::_Invoker<std::tuple<main::$_1> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4bae95)  
    #8 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() <null> (lockfree_race+0x4bae35)                                        
    #9 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() <null> (lockfree_race+0x4bad29)                                                                                                                                                                                                                              
    #10 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                                                                                                                                                                                                       
  Location is heap block of size 64 at 0x7b10000087c0 allocated by thread T1:                                                                                                                                                                                                                                                                          
    #0 operator new(unsigned long, std::align_val_t) <null> (lockfree_race+0x4b72fa)                                                                                                                                                                                                                                                                   
    #1 __gnu_cxx::new_allocator<boost::lockfree::queue<int>::node>::allocate(unsigned long, void const*) <null> (lockfree_race+0x4bbe18)                                    
    #2 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::allocate_impl<false>() <null> (lockfree_race+0x4bc6f9)  
    #3 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::allocate<true, false>() <null> (lockfree_race+0x4bc5d5)  
    #4 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::construct<true, false, int, boost::lockfree::queue<int>::node*>(int const&, boost::lockfree::queue<int>::node* const&) <null> (lockfree_race+0x4be550)  
    #5 bool boost::lockfree::queue<int>::do_push<false>(int const&) <null> (lockfree_race+0x4be209)  
    #6 boost::lockfree::queue<int>::push(int const&) <null> (lockfree_race+0x4be135)                                                                                        
    #7 main::$_0::operator()() const <null> (lockfree_race+0x4ba403)                                                                                                                                                                                                                                                                                   
    #8 void std::__invoke_impl<void, main::$_0>(std::__invoke_other, main::$_0&&) <null> (lockfree_race+0x4ba36d)                                                                                                                                                                                                                                      
    #9 std::__invoke_result<main::$_0>::type std::__invoke<main::$_0>(main::$_0&&) <null> (lockfree_race+0x4ba2bd)                                                                                                                                                                                                                                     
    #10 void std::thread::_Invoker<std::tuple<main::$_0> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4ba265)                                          
    #11 std::thread::_Invoker<std::tuple<main::$_0> >::operator()() <null> (lockfree_race+0x4ba205)                                                               
    #12 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_0> > >::_M_run() <null> (lockfree_race+0x4ba0f9)                                                                                                                                                                                                                             
    #13 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                                                                                                                                                                                                       
  Thread T2 (tid=682923, running) created by main thread at:                                                                                                                
    #0 pthread_create <null> (lockfree_race+0x425beb)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xda989)  
    #2 decltype(new ((void*)(0))std::thread(std::declval<main::$_1&>())) std::construct_at<std::thread, main::$_1&>(std::thread*, main::$_1&) <null> (lockfree_race+0x4ba9c5)  
    #3 void std::allocator_traits<std::allocator<std::thread> >::construct<std::thread, main::$_1&>(std::allocator<std::thread>&, std::thread*, main::$_1&) <null> (lockfree_race+0x4ba584)  
    #4 void std::vector<std::thread, std::allocator<std::thread> >::_M_realloc_insert<main::$_1&>(__gnu_cxx::__normal_iterator<std::thread*, std::vector<std::thread, std::allocator<std::thread> > >, main::$_1&) <null> (lockfree_race+0x4ba6c9)  
    #5 std::thread& std::vector<std::thread, std::allocator<std::thread> >::emplace_back<main::$_1&>(main::$_1&) <null> (lockfree_race+0x4b98ba)  
    #6 main <null> (lockfree_race+0x4b952a)  
  
  Thread T3 (tid=682924, running) created by main thread at:  
    #0 pthread_create <null> (lockfree_race+0x425beb)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xda989)  
    #2 decltype(new ((void*)(0))std::thread(std::declval<main::$_1&>())) std::construct_at<std::thread, main::$_1&>(std::thread*, main::$_1&) <null> (lockfree_race+0x4ba9c5)  
    #3 void std::allocator_traits<std::allocator<std::thread> >::construct<std::thread, main::$_1&>(std::allocator<std::thread>&, std::thread*, main::$_1&) <null> (lockfree_race+0x4ba584)  
    #4 void std::vector<std::thread, std::allocator<std::thread> >::_M_realloc_insert<main::$_1&>(__gnu_cxx::__normal_iterator<std::thread*, std::vector<std::thread, std::allocator<std::thread> > >, main::$_1&) <null> (lockfree_race+0x4ba6c9)  
    #5 std::thread& std::vector<std::thread, std::allocator<std::thread> >::emplace_back<main::$_1&>(main::$_1&) <null> (lockfree_race+0x4b98ba)  
    #6 main <null> (lockfree_race+0x4b9542)  
  
  Thread T1 (tid=682922, running) created by main thread at:  
    #0 pthread_create <null> (lockfree_race+0x425beb)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xda989)  
    #2 decltype(new ((void*)(0))std::thread(std::declval<main::$_0&>())) std::construct_at<std::thread, main::$_0&>(std::thread*, main::$_0&) <null> (lockfree_race+0x4b9d95)  
    #3 void std::allocator_traits<std::allocator<std::thread> >::construct<std::thread, main::$_0&>(std::allocator<std::thread>&, std::thread*, main::$_0&) <null> (lockfree_race+0x4b9954)  
    #4 void std::vector<std::thread, std::allocator<std::thread> >::_M_realloc_insert<main::$_0&>(__gnu_cxx::__normal_iterator<std::thread*, std::vector<std::thread, std::allocator<std::thread> > >, main::$_0&) <null> (lockfree_race+0x4b9a99)  
    #5 std::thread& std::vector<std::thread, std::allocator<std::thread> >::emplace_back<main::$_0&>(main::$_0&) <null> (lockfree_race+0x4b978a)  
    #6 main <null> (lockfree_race+0x4b9512)  
  
SUMMARY: ThreadSanitizer: data race (/home/chenhao/lockfree_race+0x4bc1b3) in boost::lockfree::detail::tagged_ptr<boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::freelist_node>::set_ptr(boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node,   
std::allocator<boost::lockfree::queue<int>::node> >::freelist_node*)  
==================  
==================                                                                                                                                                                                                                                                                                                                                     
WARNING: ThreadSanitizer: data race (pid=682920)                                                                                                                                                                                                                                                                                                       
  Write of size 4 at 0x7b100000ce48 by thread T1:                                                                                                                                                                                                                                                                                                      
    #0 boost::lockfree::queue<int>::node::node(int const&, boost::lockfree::queue<int>::node*) <null> (lockfree_race+0x4be835)                                                                                                                                                                                                                         
    #1 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::construct<true, false, int, boost::lockfree::queue<int>::node*>(int const&, boost::lockfree::queue<int>::node* const&) <null> (lockfree_race+0x4be5a0)                       
    #2 bool boost::lockfree::queue<int>::do_push<false>(int const&) <null> (lockfree_race+0x4be209)                                                                                                                                                                                                                                                    
    #3 boost::lockfree::queue<int>::push(int const&) <null> (lockfree_race+0x4be135)                                                                                                                                                                                                                                                                   
    #4 main::$_0::operator()() const <null> (lockfree_race+0x4ba403)                                                                                                                                                                                                                                                                                   
    #5 void std::__invoke_impl<void, main::$_0>(std::__invoke_other, main::$_0&&) <null> (lockfree_race+0x4ba36d)                                                                                                                                                                                                                                      
    #6 std::__invoke_result<main::$_0>::type std::__invoke<main::$_0>(main::$_0&&) <null> (lockfree_race+0x4ba2bd)                                                                                                                                                                                                                                     
    #7 void std::thread::_Invoker<std::tuple<main::$_0> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4ba265)                                                                                                                                                                                                                      
    #8 std::thread::_Invoker<std::tuple<main::$_0> >::operator()() <null> (lockfree_race+0x4ba205)                                                                                                                                                                                                                                                     
    #9 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_0> > >::_M_run() <null> (lockfree_race+0x4ba0f9)                                                                                                                                                                                                                              
    #10 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                                                                                                                                                                                                       
  Previous read of size 4 at 0x7b100000ce48 by thread T3:                                                                                                                                                                                                                                                                                              
    #0 void boost::lockfree::detail::copy_convertible::copy<int, int>(int&, int&) <null> (lockfree_race+0x4bd9c5)                                                                                                                                                                                                                                      
    #1 void boost::lockfree::detail::copy_payload<int, int>(int&, int&) <null> (lockfree_race+0x4bd845)                                                                                                                                                                                                                                                
    #2 bool boost::lockfree::queue<int>::pop<int>(int&) <null> (lockfree_race+0x4c0a18)                                                                                                                                                                                                                                                                
    #3 boost::lockfree::queue<int>::pop(int&) <null> (lockfree_race+0x4c07c5)                                                                                                                                                                                                                                                                          
    #4 main::$_1::operator()() const <null> (lockfree_race+0x4bb021)                                                                                                                                                                                                                                                                                   
    #5 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) <null> (lockfree_race+0x4baf9d)                                                                                                                                                                                                                                      
    #6 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) <null> (lockfree_race+0x4baeed)                                                                                                                                                                                                                                     
    #7 void std::thread::_Invoker<std::tuple<main::$_1> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4bae95)                                                                                                                                                                                                                      
    #8 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() <null> (lockfree_race+0x4bae35)                                                                                                                                                                                                                                                     
    #9 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() <null> (lockfree_race+0x4bad29)                                                                                                                                                                                                                              
    #10 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                                                                                                                                                                                                       
  Location is heap block of size 64 at 0x7b100000ce40 allocated by thread T1:                                                                                                                                                                                                                                                                          
    #0 operator new(unsigned long, std::align_val_t) <null> (lockfree_race+0x4b72fa)                                                                                                                                                                                                                                                                   
    #1 __gnu_cxx::new_allocator<boost::lockfree::queue<int>::node>::allocate(unsigned long, void const*) <null> (lockfree_race+0x4bbe18)                                                                                                                                                                                                               
    #2 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::allocate_impl<false>() <null> (lockfree_race+0x4bc6f9)                                                                                                                       
    #3 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::allocate<true, false>() <null> (lockfree_race+0x4bc5d5)                                                                                                                      
    #4 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::construct<true, false, int, boost::lockfree::queue<int>::node*>(int const&, boost::lockfree::queue<int>::node* const&) <null> (lockfree_race+0x4be550)                       
    #5 bool boost::lockfree::queue<int>::do_push<false>(int const&) <null> (lockfree_race+0x4be209)                                                                                                                                                                                                                                                    
    #6 boost::lockfree::queue<int>::push(int const&) <null> (lockfree_race+0x4be135)                                                                                                                                                                                                                                                                   
    #7 main::$_0::operator()() const <null> (lockfree_race+0x4ba403)                                                                                                                                                                                                                                                                                   
    #8 void std::__invoke_impl<void, main::$_0>(std::__invoke_other, main::$_0&&) <null> (lockfree_race+0x4ba36d)                                                                                                                                                                                                                                      
    #9 std::__invoke_result<main::$_0>::type std::__invoke<main::$_0>(main::$_0&&) <null> (lockfree_race+0x4ba2bd)                                                                                                                                                                                                                                     
    #10 void std::thread::_Invoker<std::tuple<main::$_0> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4ba265)                                                                                                                                                                                                                     
    #11 std::thread::_Invoker<std::tuple<main::$_0> >::operator()() <null> (lockfree_race+0x4ba205)                                                                                                                                                                                                                                                    
    #12 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_0> > >::_M_run() <null> (lockfree_race+0x4ba0f9)                                                                                                                                                                                                                             
    #13 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                                                                                                                                                                                                       
  Thread T1 (tid=682922, running) created by main thread at:                                                                                                                                                                                                                                                                                           
    #0 pthread_create <null> (lockfree_race+0x425beb)                                                                                                                                                                                                                                                                                                  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xda989)                                                                                                                                                                                       
    #2 decltype(new ((void*)(0))std::thread(std::declval<main::$_0&>())) std::construct_at<std::thread, main::$_0&>(std::thread*, main::$_0&) <null> (lockfree_race+0x4b9d95)                                                                                                                                                                          
    #3 void std::allocator_traits<std::allocator<std::thread> >::construct<std::thread, main::$_0&>(std::allocator<std::thread>&, std::thread*, main::$_0&) <null> (lockfree_race+0x4b9954)                                                                                                                                                            
    #4 void std::vector<std::thread, std::allocator<std::thread> >::_M_realloc_insert<main::$_0&>(__gnu_cxx::__normal_iterator<std::thread*, std::vector<std::thread, std::allocator<std::thread> > >, main::$_0&) <null> (lockfree_race+0x4b9a99)                                                                                                     
    #5 std::thread& std::vector<std::thread, std::allocator<std::thread> >::emplace_back<main::$_0&>(main::$_0&) <null> (lockfree_race+0x4b978a)                                                                                                                                                                                                       
    #6 main <null> (lockfree_race+0x4b9512)  
  
  Thread T3 (tid=682924, running) created by main thread at:  
    #0 pthread_create <null> (lockfree_race+0x425beb)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xda989)  
    #2 decltype(new ((void*)(0))std::thread(std::declval<main::$_1&>())) std::construct_at<std::thread, main::$_1&>(std::thread*, main::$_1&) <null> (lockfree_race+0x4ba9c5)  
    #3 void std::allocator_traits<std::allocator<std::thread> >::construct<std::thread, main::$_1&>(std::allocator<std::thread>&, std::thread*, main::$_1&) <null> (lockfree_race+0x4ba584)  
    #4 void std::vector<std::thread, std::allocator<std::thread> >::_M_realloc_insert<main::$_1&>(__gnu_cxx::__normal_iterator<std::thread*, std::vector<std::thread, std::allocator<std::thread> > >, main::$_1&) <null> (lockfree_race+0x4ba6c9)  
    #5 std::thread& std::vector<std::thread, std::allocator<std::thread> >::emplace_back<main::$_1&>(main::$_1&) <null> (lockfree_race+0x4b98ba)  
    #6 main <null> (lockfree_race+0x4b9542)  
  
SUMMARY: ThreadSanitizer: data race (/home/chenhao/lockfree_race+0x4be835) in boost::lockfree::queue<int>::node::node(int const&, boost::lockfree::queue<int>::node*)  
==================  
==================  
WARNING: ThreadSanitizer: data race (pid=682920)  
  Write of size 8 at 0x7b100000d940 by thread T1:  
    #0 memcpy <null> (lockfree_race+0x42fcae)  
    #1 std::atomic<boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> >::atomic(boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node>) <null> (lockfree_race+0x4bb81a)  
    #2 boost::lockfree::queue<int>::node::node(int const&, boost::lockfree::queue<int>::node*) <null> (lockfree_race+0x4be804)  
    #3 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::construct<true, false, int, boost::lockfree::queue<int>::node*>(int const&, boost::lockfree::queue<int>::node* const&) <null> (lockfree_race+0x4be5a0)  
    #4 bool boost::lockfree::queue<int>::do_push<false>(int const&) <null> (lockfree_race+0x4be209)  
    #5 boost::lockfree::queue<int>::push(int const&) <null> (lockfree_race+0x4be135)  
    #6 main::$_0::operator()() const <null> (lockfree_race+0x4ba403)  
    #7 void std::__invoke_impl<void, main::$_0>(std::__invoke_other, main::$_0&&) <null> (lockfree_race+0x4ba36d)  
    #8 std::__invoke_result<main::$_0>::type std::__invoke<main::$_0>(main::$_0&&) <null> (lockfree_race+0x4ba2bd)  
    #9 void std::thread::_Invoker<std::tuple<main::$_0> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4ba265)  
    #10 std::thread::_Invoker<std::tuple<main::$_0> >::operator()() <null> (lockfree_race+0x4ba205)  
    #11 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_0> > >::_M_run() <null> (lockfree_race+0x4ba0f9)  
    #12 <null> <null> (libstdc++.so.6+0xda6b3)  
  
  Previous atomic read of size 8 at 0x7b100000d940 by thread T3:  
    #0 __tsan_atomic64_load <null> (lockfree_race+0x4717ce)  
    #1 std::atomic<boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> >::load(std::memory_order) const <null> (lockfree_race+0x4bd37d)  
    #2 bool boost::lockfree::queue<int>::pop<int>(int&) <null> (lockfree_race+0x4c089b)  
    #3 boost::lockfree::queue<int>::pop(int&) <null> (lockfree_race+0x4c07c5)  
    #4 main::$_1::operator()() const <null> (lockfree_race+0x4bb021)  
    #5 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) <null> (lockfree_race+0x4baf9d)  
    #6 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) <null> (lockfree_race+0x4baeed)  
    #7 void std::thread::_Invoker<std::tuple<main::$_1> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4bae95)  
    #8 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() <null> (lockfree_race+0x4bae35)  
    #9 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() <null> (lockfree_race+0x4bad29)  
    #10 <null> <null> (libstdc++.so.6+0xda6b3)  
  
  Location is heap block of size 64 at 0x7b100000d940 allocated by thread T1:  
    #0 operator new(unsigned long, std::align_val_t) <null> (lockfree_race+0x4b72fa)  
    #1 __gnu_cxx::new_allocator<boost::lockfree::queue<int>::node>::allocate(unsigned long, void const*) <null> (lockfree_race+0x4bbe18)  
    #2 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::allocate_impl<false>() <null> (lockfree_race+0x4bc6f9)  
    #3 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::allocate<true, false>() <null> (lockfree_race+0x4bc5d5)  
    #4 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::construct<true, false, int, boost::lockfree::queue<int>::node*>(int const&, boost::lockfree::queue<int>::node* const&) <null> (lockfree_race+0x4be550)  
    #5 bool boost::lockfree::queue<int>::do_push<false>(int const&) <null> (lockfree_race+0x4be209)  
    #6 boost::lockfree::queue<int>::push(int const&) <null> (lockfree_race+0x4be135)  
    #7 main::$_0::operator()() const <null> (lockfree_race+0x4ba403)  
    #8 void std::__invoke_impl<void, main::$_0>(std::__invoke_other, main::$_0&&) <null> (lockfree_race+0x4ba36d)  
    #9 std::__invoke_result<main::$_0>::type std::__invoke<main::$_0>(main::$_0&&) <null> (lockfree_race+0x4ba2bd)  
    #10 void std::thread::_Invoker<std::tuple<main::$_0> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4ba265)  
    #11 std::thread::_Invoker<std::tuple<main::$_0> >::operator()() <null> (lockfree_race+0x4ba205)  
    #12 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_0> > >::_M_run() <null> (lockfree_race+0x4ba0f9)  
    #13 <null> <null> (libstdc++.so.6+0xda6b3)  
  
  Thread T1 (tid=682922, running) created by main thread at:  
    #0 pthread_create <null> (lockfree_race+0x425beb)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xda989)  
    #2 decltype(new ((void*)(0))std::thread(std::declval<main::$_0&>())) std::construct_at<std::thread, main::$_0&>(std::thread*, main::$_0&) <null> (lockfree_race+0x4b9d95)  
    #3 void std::allocator_traits<std::allocator<std::thread> >::construct<std::thread, main::$_0&>(std::allocator<std::thread>&, std::thread*, main::$_0&) <null> (lockfree_race+0x4b9954)  
    #4 void std::vector<std::thread, std::allocator<std::thread> >::_M_realloc_insert<main::$_0&>(__gnu_cxx::__normal_iterator<std::thread*, std::vector<std::thread, std::allocator<std::thread> > >, main::$_0&) <null> (lockfree_race+0x4b9a99)  
    #5 std::thread& std::vector<std::thread, std::allocator<std::thread> >::emplace_back<main::$_0&>(main::$_0&) <null> (lockfree_race+0x4b978a)  
    #6 main <null> (lockfree_race+0x4b9512)  
  
  Thread T3 (tid=682924, running) created by main thread at:  
    #0 pthread_create <null> (lockfree_race+0x425beb)  
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xda989)  
    #2 decltype(new ((void*)(0))std::thread(std::declval<main::$_1&>())) std::construct_at<std::thread, main::$_1&>(std::thread*, main::$_1&) <null> (lockfree_race+0x4ba9c5)  
    #3 void std::allocator_traits<std::allocator<std::thread> >::construct<std::thread, main::$_1&>(std::allocator<std::thread>&, std::thread*, main::$_1&) <null> (lockfree_race+0x4ba584)  
    #4 void std::vector<std::thread, std::allocator<std::thread> >::_M_realloc_insert<main::$_1&>(__gnu_cxx::__normal_iterator<std::thread*, std::vector<std::thread, std::allocator<std::thread> > >, main::$_1&) <null> (lockfree_race+0x4ba6c9)  
    #5 std::thread& std::vector<std::thread, std::allocator<std::thread> >::emplace_back<main::$_1&>(main::$_1&) <null> (lockfree_race+0x4b98ba)  
    #6 main <null> (lockfree_race+0x4b9542)  
  
SUMMARY: ThreadSanitizer: data race (/home/chenhao/lockfree_race+0x42fcae) in memcpy  
==================  
```  
  
Environment:  
```  
> clang++-12 --version  
Ubuntu clang version 12.0.0-3ubuntu1~20.04.5  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
> uname -a  
Linux chenhao 5.4.0-109-generic #123-Ubuntu SMP Fri Apr 8 09:10:54 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux  
```

---

## Comment 1

> Username: timblechmann  
> Created at: 2022-06-07 23:37:42 UTC  
> Url: https://github.com/boostorg/lockfree/issues/78#issuecomment-1149276434  

this does not totally surprise me: the dequeue operation from the michael&scott queue copies payload from a node before checking if this payload is valid: but if it reads garbage, the payload is discarded. it would be quite reasonable for tsan to consider this as data race:  
https://github.com/boostorg/lockfree/blob/develop/include/boost/lockfree/queue.hpp#L438-L443

---

## Comment 2

> Username: EikeAtOT  
> Created at: 2022-08-04 14:10:56 UTC  
> Updated at: 2022-08-04 14:23:25 UTC  
> Url: https://github.com/boostorg/lockfree/issues/78#issuecomment-1205313103  

@timblechmann : I can see how your argument would explain the following TSAN error  
  
```  
Write of size 4 at 0x7b100000ce48 by thread T1:                                                                                                                                                                                                                                                                                                      
    #0 boost::lockfree::queue<int>::node::node(int const&, boost::lockfree::queue<int>::node*) <null> (lockfree_race+0x4be835)                                                                                                                                                                                                                         
    #1 boost::lockfree::queue<int>::node* boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::construct<true, false, int, boost::lockfree::queue<int>::node*>(int const&, boost::lockfree::queue<int>::node* const&) <null> (lockfree_race+0x4be5a0)                       
    #2 bool boost::lockfree::queue<int>::do_push<false>(int const&) <null> (lockfree_race+0x4be209)                                                                                                                                                                                                                                                    
    #3 boost::lockfree::queue<int>::push(int const&) <null> (lockfree_race+0x4be135)                                                                                                                                                                                                                                                                   
    #4 main::$_0::operator()() const <null> (lockfree_race+0x4ba403)                                                                                                                                                                                                                                                                                   
    #5 void std::__invoke_impl<void, main::$_0>(std::__invoke_other, main::$_0&&) <null> (lockfree_race+0x4ba36d)                                                                                                                                                                                                                                      
    #6 std::__invoke_result<main::$_0>::type std::__invoke<main::$_0>(main::$_0&&) <null> (lockfree_race+0x4ba2bd)                                                                                                                                                                                                                                     
    #7 void std::thread::_Invoker<std::tuple<main::$_0> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4ba265)                                                                                                                                                                                                                      
    #8 std::thread::_Invoker<std::tuple<main::$_0> >::operator()() <null> (lockfree_race+0x4ba205)                                                                                                                                                                                                                                                     
    #9 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_0> > >::_M_run() <null> (lockfree_race+0x4ba0f9)                                                                                                                                                                                                                              
    #10 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                                                                                                                                                                                                       
  Previous read of size 4 at 0x7b100000ce48 by thread T3:                                                                                                                                                                                                                                                                                              
    #0 void boost::lockfree::detail::copy_convertible::copy<int, int>(int&, int&) <null> (lockfree_race+0x4bd9c5)                                                                                                                                                                                                                                      
    #1 void boost::lockfree::detail::copy_payload<int, int>(int&, int&) <null> (lockfree_race+0x4bd845)                                                                                                                                                                                                                                                
    #2 bool boost::lockfree::queue<int>::pop<int>(int&) <null> (lockfree_race+0x4c0a18)                                                                                                                                                                                                                                                                
    #3 boost::lockfree::queue<int>::pop(int&) <null> (lockfree_race+0x4c07c5)                                                                                                                                                                                                                                                                          
    #4 main::$_1::operator()() const <null> (lockfree_race+0x4bb021)                                                                                                                                                                                                                                                                                   
    #5 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) <null> (lockfree_race+0x4baf9d)                                                                                                                                                                                                                                      
    #6 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) <null> (lockfree_race+0x4baeed)                                                                                                                                                                                                                                     
    #7 void std::thread::_Invoker<std::tuple<main::$_1> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4bae95)                                                                                                                                                                                                                      
    #8 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() <null> (lockfree_race+0x4bae35)                                                                                                                                                                                                                                                     
    #9 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() <null> (lockfree_race+0x4bad29)                                                                                                                                                                                                                              
    #10 <null> <null> (libstdc++.so.6+0xda6b3)           
```  
  
But somehow it looks like there are multiple issues in those TSAN report. Can you elaborate on how that would relate to the following stacktrace (it's the very first initially reported):  
  
```  
  Write of size 8 at 0x7b10000087c0 by thread T2:                                                                                                                           
    #0 boost::lockfree::detail::tagged_ptr<boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::freelist_node>::set_ptr(boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::freelist_node*) <n  
ull> (lockfree_race+0x4bc1b3)                                                                                                                                               
    #1 boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::deallocate_impl(boost::lockfree::queue<int>::node*) <null> (lockfree_race+0x4c0cc6)  
    #2 void boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::deallocate<true>(boost::lockfree::queue<int>::node*) <null> (lockfree_race+0x4c0bd5)  
    #3 void boost::lockfree::detail::freelist_stack<boost::lockfree::queue<int>::node, std::allocator<boost::lockfree::queue<int>::node> >::destruct<true>(boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> const&) <null> (lockfree_race+0x4c0b7a)  
    #4 bool boost::lockfree::queue<int>::pop<int>(int&) <null> (lockfree_race+0x4c0aba)                                    
    #5 boost::lockfree::queue<int>::pop(int&) <null> (lockfree_race+0x4c07c5)                                                                                               
    #6 main::$_1::operator()() const <null> (lockfree_race+0x4bb021)  
    #7 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) <null> (lockfree_race+0x4baf9d)  
    #8 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) <null> (lockfree_race+0x4baeed)  
    #9 void std::thread::_Invoker<std::tuple<main::$_1> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4bae95)         
    #10 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() <null> (lockfree_race+0x4bae35)                                                                                                                                                                                                                                                    
    #11 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() <null> (lockfree_race+0x4bad29)                                                                                                                                                                                                                             
    #12 <null> <null> (libstdc++.so.6+0xda6b3)                                                                                                                                                                                                                                                                                                         
                                                                                                                                                                            
  Previous atomic read of size 8 at 0x7b10000087c0 by thread T3:                                                                                                            
    #0 __tsan_atomic64_load <null> (lockfree_race+0x4717ce)                                                                                                                 
    #1 std::atomic<boost::lockfree::detail::tagged_ptr<boost::lockfree::queue<int>::node> >::load(std::memory_order) const <null> (lockfree_race+0x4bd37d)  
    #2 bool boost::lockfree::queue<int>::pop<int>(int&) <null> (lockfree_race+0x4c089b)                             
    #3 boost::lockfree::queue<int>::pop(int&) <null> (lockfree_race+0x4c07c5)                                                                                               
    #4 main::$_1::operator()() const <null> (lockfree_race+0x4bb021)                                                                                                        
    #5 void std::__invoke_impl<void, main::$_1>(std::__invoke_other, main::$_1&&) <null> (lockfree_race+0x4baf9d)                                                           
    #6 std::__invoke_result<main::$_1>::type std::__invoke<main::$_1>(main::$_1&&) <null> (lockfree_race+0x4baeed)  
    #7 void std::thread::_Invoker<std::tuple<main::$_1> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (lockfree_race+0x4bae95)  
    #8 std::thread::_Invoker<std::tuple<main::$_1> >::operator()() <null> (lockfree_race+0x4bae35)                                        
    #9 std::thread::_State_impl<std::thread::_Invoker<std::tuple<main::$_1> > >::_M_run() <null> (lockfree_race+0x4bad29)                                                                                                                                                                                                                              
    #10 <null> <null> (libstdc++.so.6+0xda6b3)           
```  
  
To my untrained eye it looks like an atomic and non-atomic operation colliding on the same address. Moreover it looks like we are not dealing with payload here but with the free-list pointers?  
I came across those issues myself *and* I definitely had some random data corruption (most likely the same item popped twice). It would be great to really rule out that there is no issue here.  
  
Cheers,   
  
Eike  
  
EDIT: Does your argument imply that `queue::pop(U& x)` might modify `x` even if the operation fails (i.e. the function returns `false`). If so, how about stating that in the documentation?

---

## Comment 3

> Username: timblechmann  
> Created at: 2022-08-04 16:02:15 UTC  
> Url: https://github.com/boostorg/lockfree/issues/78#issuecomment-1205452379  

yes: `queue::pop` might modify the value if the dequeue operation fails. good point to add a note to the docs. it could be done without, but would add more requirements to the type (trivial ctor) and it would result in an additional memcpy  
  
---  
  
as for tsan, there are two points:  
*  the queue uses internally a node (`struct { tagged_ptr next; T payload;};`). however the tagged_ptr `next` is also used to enqueue the node onto the freelist.  
* there are quite a few cases, where tagged pointers are loaded from heap to the stack (or registers) and they may potentially be written afterwards by other threads (i guess that's when tsan detects a race condition). but e.g. `queue::pop` performs multiple reads of the "head" pointer to detect this situation and externally visible effects to the data structures are only committed via compare-and-swap

---

## Comment 4

> Username: EikeAtOT  
> Created at: 2022-08-05 07:27:00 UTC  
> Url: https://github.com/boostorg/lockfree/issues/78#issuecomment-1206133807  

Just opened a PR to add a statement to the docs: #81
