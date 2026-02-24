# #2 fixed bstree_algorithms infinite loop when iterating backwards using prev_node and added tests [Closed]

> Username: tucniak  
> Created at: 2014-03-11 14:27:40 UTC  
> Updated at: 2014-05-01 14:37:38 UTC  
> Closed at: 2014-05-01 14:37:38 UTC  
> Url: https://github.com/boostorg/intrusive/pull/2  

iterating backwards using bstree_algorithms::prev_node results in infinite loop when the tree has only one element.  
  
I've updated the code and added test cases for bstree_algorithms usage. Without the fix the test case with one element results in infinite loop, prev_node does not check if returned node is the header node  
  
I've noticed infinite loop in boost 1.49 and it looks like it's there through 1.55 up to current latest master branch.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-03-14 22:06:16 UTC  
> Url: https://github.com/boostorg/intrusive/pull/2#issuecomment-37702515  

A slightly modified test (removed unused std::function typedef) does not yield to an infinite loop with current master code but the test fails because this code is incorrect:  
  
```  
// reverse  
it = tt::rbtree_node_traits< tt::data >::get_right( & header );  
last = boost::intrusive::pointer_traits< algo::node_ptr >  
                ::const_cast_from( & header );  
BOOST_TEST( ! compEqual( it, last ) );  
BOOST_TEST( compEqual( it, 1, 'a' ) );  
it = algo::prev_node( it );  
BOOST_TEST( compEqual( it, last ) );  
```  
  
The previous node of the first node is not the header. Just like decrementing std::set::begin() iterator is undefined behaviour, there is no previous node for the first node. The header node is the "end" (one-past end) node. So next_node(last) is correct and equal to header but prev_node(last) is incorrect and undefined behaviour.

---

## Comment 2

> Username: tucniak  
> Created_at: 2014-03-15 14:28:23 UTC  
> Url: https://github.com/boostorg/intrusive/pull/2#issuecomment-37727045  

I'm not sure I see the reason why the code is incorrect?  
  
I insert one item into rbtree and then (trying to) iterate in reverse. I'm using rbtree node algorithms not `std::set` etc.  
  
Essentially the test is equivalent to:  
  
```  
template< typename T >  
    void iterateReverse( T * header, ApplyFunc func ) {  
        typedef boost::intrusive::rbtree_algorithms< rbtree_node_traits< T > > algo;  
       // get the last node  
        typename algo::node_ptr it( rbtree_node_traits< T >::get_right( header ) );  
        typename algo::node_ptr end( algo::end_node( header ) );  
        for ( ; it != end; it = algo::prev_node( it ) )  func( it );  
    }  
```  
  
There is no equivalent to reverse begin, but if the `header` node's left child points to first element and right child points to last then I should get the last node from header (right child) and then do `prev_node` until I get header - this is reverse of what `begin_node` and `next_node` is doing.  
  
Why is `prev_node(last)` incorrect and undefined - undefined where? The `last` node is the last element in the `rbtree` so it should be valid to get previous node until eventually `prev_node` returns header.   
  
Looking at the `rbtree` implementation using `next_node` we traverse in order, using `prev_node` we should be able to traverse backwards until the header, in which case `it == end`, where `end` is the header node, and `it` iterator was initialised to point to last actual node in the rbtree.  
  
I've merged in from `develop` branch.  
  
I've tested on boost 1.49, 1.55, including intrusive/develop and intrusive/master and they all end up in infinite loop when there is only one element.

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2014-03-15 14:51:54 UTC  
> Url: https://github.com/boostorg/intrusive/pull/2#issuecomment-37727654  

El 15/03/2014 15:28, tucniak escribió:  
  
> I'm not sure I see the reason why the code is incorrect?  
>   
> I insert one item into rbtree and then (trying to) iterate in reverse.  
> I'm using rbtree node algorithms not |std::set| etc.  
  
The header node is an special node. It's not part of the tree. The tree   
begins in the root node. However left/right/parent pointers are used as   
shortcuts. This is derived from the STL model.  
  
The header node is the "one-past-end" node, which is always "bigger"   
than any other node of the tree but it has no value. "left" and "right"   
pointers are shortcuts to obtain O(1) begin() and end() algorithms.   
"parent" points to the root. The header node is not the predecessor of   
the root node, but a node holding shortcuts to special nodes and also   
serves as the "end" node (which is not a logical part of the tree).   
next_node(header) and prev_node(NodeTraits::get_left(header)) are   
undefined because they iterate "outside" the [begin, end) range.  
  
This is explained in the comments before bstree_algoritms:  
  
"At the top of the tree a node is used specially. This node's parent   
pointer is pointing to the root of the tree. Its left pointer points to   
the leftmost node in the tree and the right pointer to the rightmost   
one. This node is used to represent the end-iterator."  
  
In some modern STL implementations to avoid special cases with the   
header node as the "end" node, they make the root node the left child of   
the header (so the header is naturally the successor of the biggest   
element in the tree that starts in the "root") and "parent" and "right"   
pointers are used as shortcuts to the leftmost and rightmost trees.  
  
Best,  
  
Ion

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2014-05-01 14:37:38 UTC  
> Url: https://github.com/boostorg/intrusive/pull/2#issuecomment-41915312  

I'm closing this. As explained, there is no "before_begin()" position in tree containers.

---
