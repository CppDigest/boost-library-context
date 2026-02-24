# #249 - Deque should support node `extract` and `insert` a la `std::map` (since C++17) [Closed]

> Username: justinasvd  
> Created at: 2023-06-14 05:35:13 UTC  
> Updated at: 2024-06-17 09:42:51 UTC  
> Closed at: 2024-06-14 11:50:59 UTC  
> Url: https://github.com/boostorg/container/issues/249  

Let me preface this with the clarification that this is not an issue of the `container` code. This is more of a feature request.  
  
Consider a motivating story:  
  
I have one big `deque<big_object, void, deque_options<block_size<256>>::type>`. What I want to do with this deque, I want to partition its `big_object`s to, say, 2 partitions. The first partition, naturally, may be left in the original deque, and the second partition would be put in another deque.  
  
What I would like to do, when I am adding `big_objects` to the second deque, is to steal the already moved from blocks from the first deque and insert them to the second deque, so that I can avoid the whole roundtrip through the memory allocator.  
  
Without the `extract` and `insert` the above operation requires O(n) memory (O(n) to grow deque_2, so that I can later shrink deque_1 by n). With `extract` and `insert` this partitioning would require O(1), because at most I would need to allocate 1 fresh block to put the new objects to the partition, and if any more would be added, then these blocks could be stolen from the original deque.  
  
I understand that `std::deque` did not get `extract/insert` combo like `std::map`, and I am not sure why. Probably lots of people want to steal tree nodes and less people want to steal deque nodes. But philosophically, both containers are "node" containers, and I don't see why this general approach to the node extraction is not applicable to deque.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-14 11:50:59 UTC  
> Url: https://github.com/boostorg/container/issues/249#issuecomment-2167857465  

Hi,  
  
Thanks for the proprosal, but sadly I can't see it implementable.  
  
First, you need to have an interface to a "block", which is the thing that could be moved. std::map has access to a "node" because each value is in an independent node, but a deque is a concatenation of small arrays.   
  
And the block must be full because first and last blocks might be half-full and moving this in any position of a new deque would break container invariants.  
  
Your proposal seems to be unimplementable with the current deque restrictions/design, sorry.

---

## Comment 2

> Username: justinasvd  
> Created at: 2024-06-16 06:58:09 UTC  
> Url: https://github.com/boostorg/container/issues/249#issuecomment-2171116752  

I somewhat disagree about your assessment that this is infeasible. This extraction is perfectly feasible if we extract _a previous block_ than the one pointed by an iterator. This previous block will by definition be full. So the extraction would look somewhat like this  
  
```C++  
[[nodiscard]] deque_t::val_alloc_ptr extract_prev(deque_t& deq, deque_t::iterator& it) noexcept  
{  
    using difference_type = deque_t::difference_type;  
  
    auto& members = deq.members_;  
    assert(members.m_map != nullptr);  
  
    [[maybe_unused]] const deque_t::size_type curr_size = deq.size();  
    const difference_type begin_idx = deq.begin().get_node() - members.m_map;  
    const difference_type end_idx = deq.end().get_node() - members.m_map;  
  
    const difference_type index = it.get_node() - members.m_map;  
    assert(index > begin_idx && index <= end_idx);  
  
    // Extracting prev node, so index - 1  
    auto node = members.m_map[index - 1];  
  
    // Rotate the remaining nodes  
    std::copy_backward(members.m_map + index, members.m_map + end_idx + 1, members.m_map + end_idx);  
    *(members.m_map + end_idx) = nullptr;  
  
    auto nstart = members.m_map + begin_idx;  
    auto nfinish = members.m_map + end_idx - 1;  
  
    members.m_start.priv_set_node(nstart, deq.get_block_size());  
    members.m_start.m_cur = members.m_start.m_first;  
  
    const difference_type last_block_fill = members.m_finish.m_cur - members.m_finish.m_first;  
    members.m_finish.priv_set_node(nfinish, deq.get_block_size());  
    members.m_finish.m_cur = members.m_finish.m_first + last_block_fill;  
  
    it.m_node = members.m_map + index - 1;  
  
    // After the extraction the size must change by exactly 1 block size  
    assert(deq.size() + deq.get_block_size() == curr_size);  
  
    return node;  
}  
```  
  
The post-condition of this operation is that the size of the deque shrinks by the block size, without disturbing the elements in other blocks.  
  
Now, the insert ought to be a complementing operation, perhaps the easiest way to insert an extracted block would be either to the end of the deque, or as a previous block to the one currently pointed by an iterator. Since the blocks that we extract are always full, we don't have the situation where we insert partially filled blocks.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-06-17 07:48:16 UTC  
> Url: https://github.com/boostorg/container/issues/249#issuecomment-2172541525  

I don't still understand your point. Deque can have two no-full blocks: the first and the last block. deque is by definition double-ended, so it has a symmetrical problem, so elements in the first or last used blocks can't be extracted and inserted in another position in another map.

---

## Comment 4

> Username: justinasvd  
> Created at: 2024-06-17 09:42:49 UTC  
> Url: https://github.com/boostorg/container/issues/249#issuecomment-2172901822  

What I am thinking is not the insertion to the arbitrary position, but rather `d.insert_front(node)`, `d.insert_back(node)`. The arbitrary insertion, I absolutely agree, would be O(n) and would beat the intuition that extraction and insertion should be fast operations.  
  
Now, I didn't think about the incomplete front block. I agree that the function above would not work in such a scenario. But I reckon that an incomplete block could also be extracted, then this would require to rebalance the target deque's front (or back) nodes.
