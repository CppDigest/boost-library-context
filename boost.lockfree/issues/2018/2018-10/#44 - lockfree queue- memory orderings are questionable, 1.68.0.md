# #44 - lockfree queue: memory orderings are questionable, 1.68.0 [Open]

> Username: itajsh  
> Created at: 2018-10-21 00:06:12 UTC  
> Updated at: 2020-03-12 02:30:58 UTC  
> Url: https://github.com/boostorg/lockfree/issues/44  

The possible problems are in queue::do_push and queue::pop.  
I'm not versed in the lingo of atomics and memory orderings, so my phrasing might be a bit off - but I'm quite sure my questions are correct.  
  
(1) double loads of tail_ and head_ in do_push and pop  
  
First let me mention that in the original algorithm by Scott and Michael these functions have a double load of tail and head (respectively), and then the values of the first and second load are compared.  
The purpose of these are to make sure the node has not been freed (std::free()) already, and then the &(node->next) might become a non dereferenceable address, or else its value could be randomly changed to false-positively pass the tag check.  
However in any case that the nodes are not freed but rather put in a free-list, we don’t have this problem at all.  
  
The reason they work in the algorithm, is because the algorithm is sequentially consistent memory:  
  
push: the node can be freed only after tail_ has been exchanged to a value further ahead in the list than the node itself. If the value is exchanged so in any thread, it would either be visible by the second load of tail_ or it would happen after it, due to sequential consistency.  
  
pop: basically same explanation for head_  
  
So, our queue is using weak memory ordering, therefore the above explanations do not apply.  
In case the node is actually freed, rather than put in a free-list, we need some other way to make sure &(node->next) is actually still dereferenceable.  
For example, make the second loads (of tail and head) a read-modify-write operation (not for the purpose of changing the value, but for the purpose of having a strong order of visibility with the actual value exchanges).  
  
I know that after c++11, for 14/17/20 the relationships between seq_cst and weak order operations have been strengthened. Is this what is assumed here? That the seq_cst exchanges of tail_ and head_ are strongly ordered with these second loads?  
  
(2) questionable memory ordering in do_push()  
```  
tagged_node_handle tail = tail_.load(memory_order_acquire);  
node * tail_node = pool.get_pointer(tail);  
tagged_node_handle next = tail_node->next.load(memory_order_acquire);  
node * next_ptr = pool.get_pointer(next);  
tagged_node_handle tail2 = tail_.load(memory_order_acquire);  
```  
(2.1)  
So, why would the second load of tail_ be mo_acquire?!  
What is it acquiring for?! There are no further loads after that.  
If at all it is needed, it could just as well be mo_relaxed.  
  
(2.2)  
Why would tail_node->next.load() be mo_acquire?!  
If it is acquiring for the second tail_.load(), it could only be if some other thread had a matching:  
tail_.store()  
tail_node->store( …, mo_release );  
But this doesn’t exist anywhere.  
The only node->next mo_release in the code is later in do_push() and there are no tail_.store() before it, only an tail_.exhange() after.  
  
(3) questionable memory ordering in pop()  
```  
tagged_node_handle head = head_.load(memory_order_acquire);  
node * head_ptr = pool.get_pointer(head);  
tagged_node_handle tail = tail_.load(memory_order_acquire);  
tagged_node_handle next = head_ptr->next.load(memory_order_acquire);  
node * next_ptr = pool.get_pointer(next);  
tagged_node_handle head2 = head_.load(memory_order_acquire);  
```  
(3.1)  
Why would the second head_.load be mo_aquire?!  
What is it acquiring for?  
If at all needed, it could be mo_relaxed  
  
(3.2)  
Why does head_ptr->next.load() has mo_acquire?!  
As if it’s acquiring for the second head_.load()  
But there’s no matching release for this memory.  
edit: However, it needs to be at least a mo_consume for the following reading of the payload.  
  
(4) Seems to me, in order to make sense of 2.2 and 3.2 above, maybe add   
head_ptr->next.store(..., mo_release )  
inside pop() after the head.compare_exchange succeeded.  
Then at least it would be releasing for the head exchange and also for tail exchange (because the tail exchange happens before the head exchange, in the same pop() or earlier).  
  
(5) Why does pop() copy the payload before the head.compare_exchange every loop, instead of only once after it succeeds? This is a performance difference.  
```  
detail::copy_payload(next_ptr->data, ret);  
tagged_node_handle new_head(pool.get_handle(next), head.get_next_tag());  
if (head_.compare_exchange_weak(head, new_head)) {  
  ...  
```

---

## Comment 1

> Username: itajsh  
> Created at: 2018-10-23 21:13:42 UTC  
> Updated at: 2018-10-26 22:21:30 UTC  
> Url: https://github.com/boostorg/lockfree/issues/44#issuecomment-432422063  

I want to propose the following solution. (This is a draft).  
  
edit:  
my improvement is probably more about performance than correctness (replace all mo_seq_cst to weak orderings).   
But either way, maybe the biggest problem is that it's not clear what the ordering contract of the queue is. If it deliberately intends to promise some seq_cst for the user, what exactly and why?  
My proposal should promise the followings:  
* Consider 4 actions push1, push2, pop1, pop2, these are 2 successful pushes and 2 successful pops that received these elements, executed by possibly different threads. If the call to push2 happens after the return from push1, it is impossible for the return of pop2 to happen before the call to pop1.  
* The return of pop() that successfully returned element1 happens after the call to push(element1), and after all other calls for push() for earlier elements that were in the queue.  
  
This is only in cases that the poped links go into a free-list (rather than being freed by delete/free).  
  
(1) The tag encoded in node->next within push() will be incremented from link to the following one that is pushed. Instead of as it is currently, that they increment separately for each link.  
The correct tag for the node will be stored already before the node is pushed, while the pointer value is still null.  
(2) In pop() after the head_ is successfully exchanged from pointing to node to pointing to node->next, the change of node->next has to be atomic, and also any access to this memory by the free-list has to be atomic.  
node->next.store( free_link_handle, mo_relaxed  )  
The point of this being an atomic operation is because this same node->next is possibly being concurrently read by pop() and push().  
freed_link_handle will have a tag that cannot match any tag that can be pushed in the queue. If the same location is used for the free-list next pointer/handle the tag can be prepared to fit.  
(3) All memory ordering can be weak. Specifically the tail_ and head_ compare_exchange_strong( old, new, mo_rel_ack, mo_relaxed )  
(4) to verify that the load of a tail_->next matches the tail_, instead of the double tail_.load we do:  
```  
tagged_node_handle tail = tail_.load(memory_order_acquire);  
node* tail_node = pool.get_pointer(tail);  
tagged_node_handle next = tail_node->next.load(memory_order_relaxed);  
node * next_ptr = pool.get_pointer(next);  
if( tail.get_next_tag() == next.get_tag() ) {  
  if (next_ptr == 0) {  
    tagged_node_handle new_node_next( handle_type(), next.get_next_tag()); //a null pointer with the correct tag  
    node->next.store( new_node_next, memory_order_relaxed ); //have to replace node->next tag to match the observed tail_, tail_->next  
    tagged_node_handle new_tail_next(node_handle, next.get_next_tag());  
    if ( tail_node->next.compare_exchange_weak(next, new_tail_next, memory_order_acq_rel, memory_order_relaxed) ) {  
      tagged_node_handle new_tail(node_handle, tail.get_next_tag());  
      tail_.compare_exchange_strong(tail, new_tail, memory_order_acq_rel, memory_order_relaxed);  
      return true;  
    }  
  } else {  
    tail_.compare_exchange_strong(tail, next, memory_order_acq_rel, memory_order_relaxed); //note the change here setting next without tag change. Possibly optimization acquire tail to use in next loop.  
  }  
} //else the read of tail_ and tail_->next don’t match, reloop  
```  
(5) same test for the head_->next instead of the double head_.load  
(6) memory orderings: suppose the queue had the following node sequence pushed at some point node0, node1, node2 containing data0, data1, data2  
  
- The exchange of the head_ from node1 to node2, in pop() that shall return data2, happens after   
- the tail_ has been exchanged further ahead of node1 (earlier in this pop call, or another pop or push), that is to node2 or even further. which happens after   
- node1->next pointer has been exchanged in push(data2) to point to node2, which happens after   
- the tail_ has been exchanged to point to node1 (earlier in the same push(data2) or in push(data1) or another push or pop). which happens after  
- node1 has been pushed in push(data1), that is by exchanging node0->next to point to node1. which happens after  
- node1->next tag has been stored correctly with null pointer in push(data1), which happens after   
- node1 was taken from the free-list in push(data1), which happens after   
- node1 was last put into free-list in some earlier pop(), which happens after   
- node1->next previous queue’d tag has been changed in this earlier pop()

---

## Comment 2

> Username: heartrude  
> Created at: 2020-03-12 02:00:05 UTC  
> Updated at: 2020-03-12 02:18:19 UTC  
> Url: https://github.com/boostorg/lockfree/issues/44#issuecomment-597968421  

1.72 still have this issue

---

## Comment 3

> Username: timblechmann  
> Created at: 2020-03-12 02:30:58 UTC  
> Url: https://github.com/boostorg/lockfree/issues/44#issuecomment-597975403  

```  
tagged_node_handle tail = tail_.load(memory_order_acquire);  
node * tail_node = pool.get_pointer(tail);  
tagged_node_handle next = tail_node->next.load(memory_order_acquire);  
node * next_ptr = pool.get_pointer(next);  
tagged_node_handle tail2 = tail_.load(memory_order_acquire);  
```  
  
afaict the order of the atomic loads matters here, so we cannot use `memory_order_relaxed`. in general i'm rather hesitant to touch this without running stress-tests.
