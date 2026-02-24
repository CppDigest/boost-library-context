# #766 DOC: Fixed path problems [Closed]

> Username: Kojoley  
> Created at: 2018-02-25 13:03:58 UTC  
> Updated at: 2020-05-15 20:09:17 UTC  
> Closed at: 2020-05-15 20:09:17 UTC  
> Url: https://github.com/boostorg/compute/pull/766  

Closes #652  
  
FYI: You have some other minor problems with docs:  
<details>  
  <summary>Click to expand</summary>  
  
```  
quickbook.quickbook-to-boostbook ..\bin.v2\libs\compute\doc\msvc-14.1\debug\threadapi-win32\threading-multi\compute.xml  
Generating Output File: ..\bin.v2\libs\compute\doc\msvc-14.1\debug\threadapi-win32\threading-multi\compute.xml  
doxygen-action ..\bin.v2\libs\compute\doc\none\msvc-14.1\debug\threadapi-win32\threading-multi\autodoc-xml.xml-dir  
The system cannot find the file specified.  
../boost/libs/compute/include/boost/compute/algorithm/accumulate.hpp:159: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/accumulate.hpp:160: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/adjacent_difference.hpp:67: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/adjacent_difference.hpp:68: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/adjacent_find.hpp:117: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/all_of.hpp:23: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/any_of.hpp:27: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/binary_search.hpp:24: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/copy.hpp:829: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/copy_if.hpp:42: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/copy_n.hpp:33: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/count.hpp:26: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/count.hpp:27: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/count_if.hpp:29: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/count_if.hpp:30: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/equal.hpp:24: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/equal_range.hpp:27: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/exclusive_scan.hpp:47: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/exclusive_scan.hpp:48: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/exclusive_scan.hpp:49: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/fill.hpp:274: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/fill_n.hpp:23: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/find.hpp:26: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/find_end.hpp:91: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/find_if.hpp:24: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/find_if_not.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/for_each.hpp:48: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/for_each_n.hpp:22: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/gather.hpp:65: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/generate.hpp:26: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/generate_n.hpp:24: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/includes.hpp:111: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/inclusive_scan.hpp:45: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/inclusive_scan.hpp:46: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/inclusive_scan.hpp:47: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/inner_product.hpp:30: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/inner_product.hpp:31: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/inplace_merge.hpp:27: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/iota.hpp:30: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/is_partitioned.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/is_permutation.hpp:37: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/is_sorted.hpp:33: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/lexicographical_compare.hpp:108: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/lower_bound.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/max_element.hpp:46: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/max_element.hpp:47: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/merge.hpp:40: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/minmax_element.hpp:34: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/minmax_element.hpp:35: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/min_element.hpp:46: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/min_element.hpp:47: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/mismatch.hpp:32: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/next_permutation.hpp:132: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/none_of.hpp:23: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/nth_element.hpp:27: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/partial_sum.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/partial_sum.hpp:26: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/partial_sum.hpp:27: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/partition.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/partition_copy.hpp:27: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/partition_point.hpp:30: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/prev_permutation.hpp:132: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/random_shuffle.hpp:31: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/reduce.hpp:268: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/reduce.hpp:269: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/reduce_by_key.hpp:54: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/reduce_by_key.hpp:55: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/remove.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/remove_if.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/replace.hpp:72: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/replace_copy.hpp:28: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/reverse.hpp:55: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/reverse_copy.hpp:54: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/rotate.hpp:24: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/rotate_copy.hpp:23: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/scatter.hpp:82: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/scatter_if.hpp:86: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/search.hpp:35: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/search_n.hpp:103: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/set_difference.hpp:124: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/set_intersection.hpp:112: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/set_symmetric_difference.hpp:135: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/set_union.hpp:137: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/sort.hpp:179: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/sort_by_key.hpp:131: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/stable_partition.hpp:34: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/stable_sort.hpp:75: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/stable_sort_by_key.hpp:129: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/swap_ranges.hpp:25: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/transform.hpp:32: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/transform_reduce.hpp:33: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/transform_reduce.hpp:34: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/unique.hpp:34: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/unique_copy.hpp:130: warning: Found unknown command `\Omega'  
../boost/libs/compute/include/boost/compute/algorithm/upper_bound.hpp:26: warning: Found unknown command `\Omega'  
xslt-xsltproc.windows ..\bin.v2\libs\compute\doc\none\msvc-14.1\debug\threadapi-win32\threading-multi\autodoc-xml.doxygen  
xslt-xsltproc.windows ..\bin.v2\libs\compute\doc\none\msvc-14.1\debug\threadapi-win32\threading-multi\autodoc-xml.boostbook  
../boost/libs/compute/include/boost/compute/event.hpp:318: Cannot handle sectiondef with kind=protected-attrib  
../boost/libs/compute/include/boost/compute/memory_object.hpp:245: Cannot handle sectiondef with kind=protected-attrib  
common.copy ..\boost\libs\compute\doc\autodoc.xml  
..\bin.v2\libs\compute\doc\none\msvc-14.1\debug\threadapi-win32\threading-multi\autodoc-xml.boostbook  
        1 file(s) copied.  
xslt-xsltproc.windows ..\bin.v2\libs\compute\doc\msvc-14.1\debug\threadapi-win32\threading-multi\compute.docbook  
Reference to method 'boost::compute::buffer::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::command_queue::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::command_queue::enqueue_nd_range_kernel' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::command_queue::enqueue_barrier' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::command_queue::enqueue_marker' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::context::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::device::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::event::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::kernel::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::kernel::get_arg_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::kernel::set_arg' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::pipe::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::platform::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::program::create_with_source' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::program::create_with_binary' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Reference to method 'boost::compute::program::get_info' is ambiguous. Found:  
              Match in namespace ::  
              Match in namespace ::  
Cannot find class named 'vector_type::iterator'  
Cannot find class named 'vector_type::const_iterator'  
Cannot find class named 'placeholder'  
Cannot find class named 'placeholder'  
Cannot find class named 'placeholder'  
Cannot find class named 'placeholder'  
xslt-xsltproc-dir.windows ..\boost\libs\compute\doc\html\standalone_HTML.manifest  
```  
  
</details>

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-05-15 20:09:17 UTC  
> Url: https://github.com/boostorg/compute/pull/766#issuecomment-629458169  

I am closing this as there is no response

---
