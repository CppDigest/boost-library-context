# #4 - binomial heaps sanity checks are wrong [Open]

> Username: avikivity  
> Created at: 2015-12-28 15:17:50 UTC  
> Updated at: 2015-12-28 15:20:23 UTC  
> Url: https://github.com/boostorg/heap/issues/4  

```  
    template <typename Compare>  
    void siftup(node_pointer n, Compare const & cmp)  
    {  
        while (n->parent) {  
            node_pointer parent = n->parent;  
            node_pointer grand_parent = parent->parent;  
            if (cmp(n->value, parent->value))  
                return;  
  
            n->remove_from_parent();  
  
            n->swap_children(parent);  
            n->update_children();  
            parent->update_children();  
  
            if (grand_parent) {  
                parent->remove_from_parent();  
                grand_parent->add_child(n);  
            } else {  
                node_list_iterator it = trees.erase(node_list_type::s_iterator_to(*parent));  
                trees.insert(it, *n);  
            }  
            n->add_child(parent);  
            BOOST_HEAP_ASSERT(parent->child_count() == n->child_count());  
        }  
    }  
```  
  
How can the parent child count equal the child count?  Obviously the parent has at least one more child than the parent.  
  
```  
    void sanity_check(void)  
    {  
#ifdef BOOST_HEAP_SANITYCHECKS  
        sorted_by_degree();  
  
        if (!empty()) {  
            node_pointer found_top = detail::find_max_child<node_list_type, node_type, internal_compare>(trees, super_t::get_internal_cmp());  
            BOOST_HEAP_ASSERT(top_element == found_top);  
        }  
  
        if (constant_time_size) {  
            size_t counted = detail::count_list_nodes<node_type, node_list_type>(trees);  
            size_t stored = size_holder::get_size();  
            BOOST_HEAP_ASSERT(counted == stored);  
        }  
#endif  
    }  
```  
  
What happens if there are two equivalent elements?  One might be `top_element` and the other calculated in `found_top`.  We need to compare them via the comparator, not via node identity.

---

## Comment 1

> Username: avikivity  
> Created at: 2015-12-28 15:18:55 UTC  
> Url: https://github.com/boostorg/heap/issues/4#issuecomment-167586851  

Suggest running the regression tests with `BOOST_HEAP_SANITYCHECKS` defined.
