# #111 - error: 'uint16_t' in namespace 'std' does not name a type [Closed]

> Username: andreas-schwab  
> Created at: 2025-03-10 16:01:26 UTC  
> Updated at: 2025-03-11 13:08:28 UTC  
> Closed at: 2025-03-11 13:08:28 UTC  
> Url: https://github.com/boostorg/lockfree/issues/111  

$ g++ queue.cpp   
In file included from /usr/include/boost/lockfree/queue.hpp:23,  
                 from queue.cpp:8:  
/usr/include/boost/lockfree/detail/freelist.hpp:274:18: error: 'uint16_t' in namespace 'std' does not name a type; did you mean 'wint_t'?  
  274 |     typedef std::uint16_t tag_t;  
      |                  ^~~~~~~~  
      |                  wint_t  
/usr/include/boost/lockfree/detail/freelist.hpp:275:18: error: 'uint16_t' in namespace 'std' does not name a type; did you mean 'wint_t'?  
  275 |     typedef std::uint16_t index_t;  
      |                  ^~~~~~~~  
      |                  wint_t  
/usr/include/boost/lockfree/detail/freelist.hpp:284:35: error: expected ')' before 'i'  
  284 |     explicit tagged_index( index_t i, tag_t t = 0 ) :  
      |                          ~        ^~  
      |                                   )  
/usr/include/boost/lockfree/detail/freelist.hpp:291:5: error: 'index_t' does not name a type  
  291 |     index_t get_index() const  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:296:21: error: 'index_t' has not been declared  
  296 |     void set_index( index_t i )  
      |                     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:304:5: error: 'tag_t' does not name a type  
  304 |     tag_t get_tag() const  
      |     ^~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:309:5: error: 'tag_t' does not name a type  
  309 |     tag_t get_next_tag() const  
      |     ^~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:315:19: error: 'tag_t' has not been declared  
  315 |     void set_tag( tag_t t )  
      |                   ^~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:332:5: error: 'index_t' does not name a type  
  332 |     index_t index;  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:333:5: error: 'tag_t' does not name a type  
  333 |     tag_t   tag;  
      |     ^~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'void boost::lockfree::detail::tagged_index::set_index(int)':  
/usr/include/boost/lockfree/detail/freelist.hpp:298:17: error: overloaded function with no contextual type information  
  298 |         index = i;  
      |                 ^  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'void boost::lockfree::detail::tagged_index::set_tag(int)':  
/usr/include/boost/lockfree/detail/freelist.hpp:317:13: error: expected primary-expression before '=' token  
  317 |         tag = t;  
      |             ^  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'bool boost::lockfree::detail::tagged_index::operator==(const boost::lockfree::detail::tagged_index&) const':  
/usr/include/boost/lockfree/detail/freelist.hpp:323:31: error: 'const class boost::lockfree::detail::tagged_index' has no member named 'index'  
  323 |         return ( index == rhs.index ) && ( tag == rhs.tag );  
      |                               ^~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:323:48: error: expected primary-expression before '==' token  
  323 |         return ( index == rhs.index ) && ( tag == rhs.tag );  
      |                                                ^~  
/usr/include/boost/lockfree/detail/freelist.hpp:323:55: error: 'const class boost::lockfree::detail::tagged_index' has no member named 'tag'  
  323 |         return ( index == rhs.index ) && ( tag == rhs.tag );  
      |                                                       ^~~  
/usr/include/boost/lockfree/detail/freelist.hpp: At global scope:  
/usr/include/boost/lockfree/detail/freelist.hpp:430:27: error: 'index_t' in 'class boost::lockfree::detail::tagged_index' does not name a type  
  430 |     typedef tagged_index::index_t index_t;  
      |                           ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:516:5: error: 'index_t' does not name a type  
  516 |     index_t null_handle( void ) const  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:521:5: error: 'index_t' does not name a type  
  521 |     index_t get_handle( T* pointer ) const  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:529:5: error: 'index_t' does not name a type  
  529 |     index_t get_handle( tagged_node_handle const& handle ) const  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:539:21: error: 'index_t' has not been declared  
  539 |     T* get_pointer( index_t index ) const  
      |                     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:554:5: error: 'index_t' does not name a type  
  554 |     index_t allocate( void )  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:563:5: error: 'index_t' does not name a type  
  563 |     index_t allocate_impl( void )  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:582:5: error: 'index_t' does not name a type  
  582 |     index_t allocate_impl_unsafe( void )  
      |     ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:600:22: error: 'index_t' has not been declared  
  600 |     void deallocate( index_t index )  
      |                      ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:608:27: error: 'index_t' has not been declared  
  608 |     void deallocate_impl( index_t index )  
      |                           ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:622:34: error: 'index_t' has not been declared  
  622 |     void deallocate_impl_unsafe( index_t index )  
      |                                  ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'void boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::initialize()':  
/usr/include/boost/lockfree/detail/freelist.hpp:418:36: error: there are no arguments to 'null_handle' that depend on a template parameter, so a declaration of 'null_handle' must be available [-fpermissive]  
  418 |             next_index->set_index( null_handle() );  
      |                                    ^~~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:418:36: note: (if you use '-fpermissive', G++ will accept your code, but allowing the use of an undeclared name is deprecated)  
/usr/include/boost/lockfree/detail/freelist.hpp:423:47: error: 'index_t' does not name a type  
  423 |             deallocate< false >( static_cast< index_t >( i ) );  
      |                                               ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In constructor 'boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::fixed_size_freelist(const Allocator&, std::size_t)':  
/usr/include/boost/lockfree/detail/freelist.hpp:435:43: error: 'index_t' was not declared in this scope; did you mean 'index'?  
  435 |         pool_( tagged_index( static_cast< index_t >( count ), 0 ) )  
      |                                           ^~~~~~~  
      |                                           index  
/usr/include/boost/lockfree/detail/freelist.hpp:435:28: error: expected primary-expression before '(' token  
  435 |         pool_( tagged_index( static_cast< index_t >( count ), 0 ) )  
      |                            ^  
/usr/include/boost/lockfree/detail/freelist.hpp:435:43: error: 'index_t' does not name a type  
  435 |         pool_( tagged_index( static_cast< index_t >( count ), 0 ) )  
      |                                           ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'T* boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::construct()':  
/usr/include/boost/lockfree/detail/freelist.hpp:449:9: error: 'index_t' was not declared in this scope; did you mean 'index'?  
  449 |         index_t node_index = allocate< ThreadSafe >();  
      |         ^~~~~~~  
      |         index  
/usr/include/boost/lockfree/detail/freelist.hpp:450:14: error: 'node_index' was not declared in this scope  
  450 |         if ( node_index == null_handle() )  
      |              ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:450:28: error: there are no arguments to 'null_handle' that depend on a template parameter, so a declaration of 'null_handle' must be available [-fpermissive]  
  450 |         if ( node_index == null_handle() )  
      |                            ^~~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:453:42: error: 'node_index' was not declared in this scope  
  453 |         T* node = NodeStorage::nodes() + node_index;  
      |                                          ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'T* boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::construct(const ArgumentType&)':  
/usr/include/boost/lockfree/detail/freelist.hpp:461:9: error: 'index_t' was not declared in this scope; did you mean 'index'?  
  461 |         index_t node_index = allocate< ThreadSafe >();  
      |         ^~~~~~~  
      |         index  
/usr/include/boost/lockfree/detail/freelist.hpp:462:14: error: 'node_index' was not declared in this scope  
  462 |         if ( node_index == null_handle() )  
      |              ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:462:28: error: there are no arguments to 'null_handle' that depend on a template parameter, so a declaration of 'null_handle' must be available [-fpermissive]  
  462 |         if ( node_index == null_handle() )  
      |                            ^~~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:465:42: error: 'node_index' was not declared in this scope  
  465 |         T* node = NodeStorage::nodes() + node_index;  
      |                                          ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'T* boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::construct(ArgumentType&&)':  
/usr/include/boost/lockfree/detail/freelist.hpp:473:9: error: 'index_t' was not declared in this scope; did you mean 'index'?  
  473 |         index_t node_index = allocate< ThreadSafe >();  
      |         ^~~~~~~  
      |         index  
/usr/include/boost/lockfree/detail/freelist.hpp:474:14: error: 'node_index' was not declared in this scope  
  474 |         if ( node_index == null_handle() )  
      |              ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:474:28: error: there are no arguments to 'null_handle' that depend on a template parameter, so a declaration of 'null_handle' must be available [-fpermissive]  
  474 |         if ( node_index == null_handle() )  
      |                            ^~~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:477:42: error: 'node_index' was not declared in this scope  
  477 |         T* node = NodeStorage::nodes() + node_index;  
      |                                          ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'T* boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::construct(const ArgumentType1&, const ArgumentType2&)':  
/usr/include/boost/lockfree/detail/freelist.hpp:485:9: error: 'index_t' was not declared in this scope; did you mean 'index'?  
  485 |         index_t node_index = allocate< ThreadSafe >();  
      |         ^~~~~~~  
      |         index  
/usr/include/boost/lockfree/detail/freelist.hpp:486:14: error: 'node_index' was not declared in this scope  
  486 |         if ( node_index == null_handle() )  
      |              ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:486:28: error: there are no arguments to 'null_handle' that depend on a template parameter, so a declaration of 'null_handle' must be available [-fpermissive]  
  486 |         if ( node_index == null_handle() )  
      |                            ^~~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:489:42: error: 'node_index' was not declared in this scope  
  489 |         T* node = NodeStorage::nodes() + node_index;  
      |                                          ^~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'void boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::destruct(tagged_node_handle)':  
/usr/include/boost/lockfree/detail/freelist.hpp:497:9: error: 'index_t' was not declared in this scope; did you mean 'index'?  
  497 |         index_t index = tagged_index.get_index();  
      |         ^~~~~~~  
      |         index  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'void boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::destruct(T*)':  
/usr/include/boost/lockfree/detail/freelist.hpp:508:48: error: 'index_t' does not name a type  
  508 |         deallocate< ThreadSafe >( static_cast< index_t >( n - NodeStorage::nodes() ) );  
      |                                                ^~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'T* boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::get_pointer(const tagged_node_handle&) const':  
/usr/include/boost/lockfree/detail/freelist.hpp:536:34: error: 'const boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::tagged_node_handle' {aka 'const class boost::lockfree::detail::tagged_index'} has no member named 'get_inde '; did you mean 'set_index'?  
  536 |         return get_pointer( tptr.get_index() );  
      |                                  ^~~~~~~~~  
      |                                  set_index  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'T* boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::get_pointer(int) const':  
/usr/include/boost/lockfree/detail/freelist.hpp:541:23: error: there are no arguments to 'null_handle' that depend on a template parameter, so a declaration of 'null_handle' must be available [-fpermissive]  
  541 |         if ( index == null_handle() )  
      |                       ^~~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'void boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::deallocate_impl(int)':  
/usr/include/boost/lockfree/detail/freelist.hpp:614:52: error: 'class boost::lockfree::detail::tagged_index' has no member named 'get_tag'; did you mean 'set_tag'?  
  614 |             tagged_index new_pool( index, old_pool.get_tag() );  
      |                                                    ^~~~~~~  
      |                                                    set_tag  
/usr/include/boost/lockfree/detail/freelist.hpp:615:53: error: 'class boost::lockfree::detail::tagged_index' has no member named 'get_index'; did you mean 'set_index'?  
  615 |             new_pool_node->next.set_index( old_pool.get_index() );  
      |                                                     ^~~~~~~~~  
      |                                                     set_index  
/usr/include/boost/lockfree/detail/freelist.hpp: In member function 'void boost::lockfree::detail::fixed_size_freelist<T, NodeStorage>::deallocate_impl_unsafe(int)':  
/usr/include/boost/lockfree/detail/freelist.hpp:627:48: error: 'class boost::lockfree::detail::tagged_index' has no member named 'get_tag'; did you mean 'set_tag'?  
  627 |         tagged_index new_pool( index, old_pool.get_tag() );  
      |                                                ^~~~~~~  
      |                                                set_tag  
/usr/include/boost/lockfree/detail/freelist.hpp:628:49: error: 'class boost::lockfree::detail::tagged_index' has no member named 'get_index'; did you mean 'set_index'?  
  628 |         new_pool_node->next.set_index( old_pool.get_index() );  
      |                                                 ^~~~~~~~~  
      |                                                 set_index  
/usr/include/boost/lockfree/detail/freelist.hpp: At global scope:  
/usr/include/boost/lockfree/detail/freelist.hpp:661:73: error: 'index_t' in 'class boost::lockfree::detail::tagged_index' does not name a type  
  661 | std::conditional_t< IsNodeBased, T*, typename tagged_index::index_t > handle_type;  
      |                                                             ^~~~~~~  
  
/usr/include/boost/lockfree/detail/freelist.hpp:661:81: error: template argument 3 is invalid  
  661 | ditional_t< IsNodeBased, T*, typename tagged_index::index_t > handle_type;  
      |                                                             ^  
  
/usr/include/boost/lockfree/detail/freelist.hpp: In instantiation of 'T* boost::lockfree::detail::freelist_stack<T, Alloc>::construct(ArgumentType&&) [with bool ThreadSafe = true; bool Bounded = false; ArgumentType = boost::lockfree::queue<int>::node*; T = boost::lockfree::queue<int>::node; Alloc = boost::alignment::aligned_allocator<boost::lockfree::queue<int>::node, 64>]':  
/usr/include/boost/lockfree/queue.hpp:139:70:   required from 'void boost::lockfree::queue<T, Options>::initialize() [with T = int; Options = {}]'  
  139 | ode*              n = pool.template construct< true, false >( pool.null_handle() );  
      |                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~  
  
/usr/include/boost/lockfree/queue.hpp:228:9:   required from 'boost::lockfree::queue<T, Options>::queue(size_type) [with Enabler = std::enable_if<true, void>; T = int; Options = {}; size_type = long unsigned int]'  
  228 |         initialize();  
      |         ^~~~~~~~~~  
queue.cpp:17:42:   required from here  
   17 | boost::lockfree::queue< int > queue( 128 );  
      |                                          ^  
/usr/include/boost/lockfree/detail/freelist.hpp:101:13: error: invalid conversion from 'boost::lockfree::queue<int>::node*' to 'boost::lockfree::queue<int>::node::handle_type' {aka 'int'} [-fpermissive]  
  101 |             new ( node ) T( std::forward< ArgumentType >( arg ) );  
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
      |             |  
      |             boost::lockfree::queue<int>::node*  
/usr/include/boost/lockfree/queue.hpp:121:27: note:   initializing argument 1 of 'boost::lockfree::queue<T, Options>::node::node(handle_type) [with T = int; Options = {}; handle_type = int]'  
  121 |         node( handle_type null_handle ) :  
      |               ~~~~~~~~~~~~^~~~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp: In instantiation of 'T* boost::lockfree::detail::freelist_stack<T, Alloc>::construct(ArgumentType1&&, ArgumentType2&&) [with bool ThreadSafe = true; bool Bounded = false; ArgumentType1 = const int&; ArgumentType2 = boost::lockfree::queue<int>::node*; T = boost::lockfree::queue<int>::node; Alloc = boost::alignment::aligned_allocator<boost::lockfree::queue<int>::node, 64>]':  
/usr/include/boost/lockfree/queue.hpp:351:59:   required from 'bool boost::lockfree::queue<T, Options>::do_push(const T&) [with bool Bounded = false; T = int; Options = {}]'  
  351 |         node* n = pool.template construct< true, Bounded >( t, pool.null_handle() );  
      |                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/lockfree/queue.hpp:310:32:   required from 'bool boost::lockfree::queue<T, Options>::push(const T&) [with T = int; Options = {}]'  
  310 |         return do_push< false >( t );  
      |                ~~~~~~~~~~~~~~~~^~~~~  
queue.cpp:27:28:   required from here  
   27 |         while ( !queue.push( value ) )  
      |                  ~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/lockfree/detail/freelist.hpp:110:13: error: invalid conversion from 'boost::lockfree::queue<int>::node*' to 'boost::lockfree::queue<int>::node::handle_type' {aka 'int'} [-fpermissive]  
  110 |             new ( node ) T( arg1, arg2 );  
      |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
      |             |  
      |             boost::lockfree::queue<int>::node*  
/usr/include/boost/lockfree/queue.hpp:112:39: note:   initializing argument 2 of 'boost::lockfree::queue<T, Options>::node::node(const T&, handle_type) [with T = int; Options = {}; handle_type = int]'  
  112 |         node( T const& v, handle_type null_handle ) :  
      |                           ~~~~~~~~~~~~^~~~~~~~~~~  
/usr/include/boost/lockfree/queue.hpp: In instantiation of 'bool boost::lockfree::queue<T, Options>::do_push_node(node*) [with T = int; Options = {}]':  
/usr/include/boost/lockfree/queue.hpp:352:16:   required from 'bool boost::lockfree::queue<T, Options>::do_push(const T&) [with bool Bounded = false; T = int; Options = {}]'  
  352 |         return do_push_node( n );  
      |                ^~~~~~~~~~~~  
/usr/include/boost/lockfree/queue.hpp:310:32:   required from 'bool boost::lockfree::queue<T, Options>::push(const T&) [with T = int; Options = {}]'  
  310 |         return do_push< false >( t );  
      |                ~~~~~~~~~~~~~~~~^~~~~  
queue.cpp:27:28:   required from here  
   27 |         while ( !queue.push( value ) )  
      |                  ~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/lockfree/queue.hpp:357:50: error: invalid conversion from 'boost::lockfree::queue<int>::node*' to 'boost::lockfree::queue<int>::handle_type' {aka 'int'} [-fpermissive]  
  357 |         handle_type node_handle = pool.get_handle( n );  
      |                                   ~~~~~~~~~~~~~~~^~~~~  
      |                                                  |  
      |                                                  boost::lockfree::queue<int>::node*  
/usr/include/boost/lockfree/queue.hpp:371:55: error: invalid conversion from 'boost::lockfree::queue<int>::handle_type' {aka 'int'} to 'boost::lockfree::queue<int>::node*' [-fpermissive]  
  371 |                     tagged_node_handle new_tail_next( node_handle, next.get_next_tag() );  
      |                                                       ^~~~~~~~~~~  
      |                                                       |  
      |                                                       boost::lockfree::queue<int>::handle_type {aka int}  
In file included from /usr/include/boost/lockfree/detail/tagged_ptr.hpp:16,  
                 from /usr/include/boost/lockfree/detail/freelist.hpp:26:  
/usr/include/boost/lockfree/detail/tagged_ptr_dcas.hpp:41:29: note:   initializing argument 1 of 'boost::lockfree::detail::tagged_ptr<T>::tagged_ptr(T*, tag_t) [with T = boost::lockfree::queue<int>::node; tag_t = long unsigned int]'  
   41 |     explicit tagged_ptr( T* p, tag_t t = 0 ) :  
      |                          ~~~^  
/usr/include/boost/lockfree/queue.hpp:373:54: error: invalid conversion from 'boost::lockfree::queue<int>::handle_type' {aka 'int'} to 'boost::lockfree::queue<int>::node*' [-fpermissive]  
  373 |                         tagged_node_handle new_tail( node_handle, tail.get_next_tag() );  
      |                                                      ^~~~~~~~~~~  
      |                                                      |  
      |                                                      boost::lockfree::queue<int>::handle_type {aka int}  
/usr/include/boost/lockfree/detail/tagged_ptr_dcas.hpp:41:29: note:   initializing argument 1 of 'boost::lockfree::detail::tagged_ptr<T>::tagged_ptr(T*, tag_t) [with T = boost::lockfree::queue<int>::node; tag_t = long unsigned int]'  
   41 |     explicit tagged_ptr( T* p, tag_t t = 0 ) :  
      |                          ~~~^

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-03-11 00:18:27 UTC  
> Url: https://github.com/boostorg/lockfree/issues/111#issuecomment-2712138256  

which compiler (version) and which boost version are you using?

---

## Comment 2

> Username: andreas-schwab  
> Created at: 2025-03-11 08:46:42 UTC  
> Url: https://github.com/boostorg/lockfree/issues/111#issuecomment-2713185040  

gcc 14.2.1  
boost 1.87.0
