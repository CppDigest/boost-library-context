# #52 - error: cannot call member function impl() of impl_pointer in boost [Closed]

> Username: gp1322719830  
> Created at: 2021-11-30 09:59:08 UTC  
> Updated at: 2021-12-08 17:01:24 UTC  
> Closed at: 2021-12-08 17:01:24 UTC  
> Url: https://github.com/boostorg/multi_index/issues/52  

I am trying to build a version of Caffe for GPU on Ubuntu 18.04 with boost 1.67 an CUDA 10.1  
  
I checked out the code and I am getting the error cannot call member function ‘boost::multi_index::detail::sequenced_index_node<Super>::impl_pointer boost::multi_index::detail::sequenced_index_node<Super>::impl() which seems to be related to boost library and I have installed boost as a dependency:  
  
NVCC src/caffe/layers/cudnn_conv_layer.cu  
/usr/include/boost/multi_index/detail/seq_index_node.hpp: In instantiation of ‘static void boost::multi_index::detail::sequenced_index_node<Super>::increment(boost::multi_index::detail::sequenced_index_node<Super>*&) [with Super = boost::multi_index::detail::ordered_index_node<boost::multi_index::detail::null_augment_policy, boost::multi_index::detail::index_node_base<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > > > >]’:  
/usr/include/boost/multi_index/detail/bidir_node_iterator.hpp:55:16:   required from ‘boost::multi_index::detail::bidir_node_iterator<Node>& boost::multi_index::detail::bidir_node_iterator<Node>::operator++() [with Node = boost::multi_index::detail::sequenced_index_node<boost::multi_index::detail::ordered_index_node<boost::multi_index::detail::null_augment_policy, boost::multi_index::detail::index_node_base<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > > > > >]’  
/usr/include/boost/multi_index_container.hpp:269:73:   required from ‘boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>::multi_index_container(const boost::multi_index::multi_index_container<Value, IndexSpecifierList, Allocator>&) [with Value = std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >; IndexSpecifierList = boost::multi_index::indexed_by<boost::multi_index::sequenced<boost::multi_index::tag<> >, boost::multi_index::ordered_non_unique<boost::multi_index::tag<boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >::subs::by_name, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::member<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, const std::__cxx11::basic_string<char>, &std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >::first>, std::less<std::__cxx11::basic_string<char> > >, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>; Allocator = std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >]’  
/usr/include/boost/property_tree/detail/ptree_implementation.hpp:191:94:   required from ‘boost::property_tree::basic_ptree<Key, Data, KeyCompare>::basic_ptree(const boost::property_tree::basic_ptree<Key, Data, KeyCompare>&) [with Key = std::__cxx11::basic_string<char>; Data = std::__cxx11::basic_string<char>; KeyCompare = std::less<std::__cxx11::basic_string<char> >]’  
src/caffe/layers/detection_output_layer.cu:220:29:   required from ‘void caffe::DetectionOutputLayer<Dtype>::Forward_gpu(const std::vector<caffe::Blob<Dtype>*>&, const std::vector<caffe::Blob<Dtype>*>&) [with Dtype = float]’  
src/caffe/layers/detection_output_layer.cu:302:147:   required from here  
/usr/include/boost/multi_index/detail/seq_index_node.hpp:198:23: error: cannot call member function ‘boost::multi_index::detail::sequenced_index_node<Super>::impl_pointer boost::multi_index::detail::sequenced_index_node<Super>::impl() [with Super = boost::multi_index::detail::ordered_index_node<boost::multi_index::detail::null_augment_policy, boost::multi_index::detail::index_node_base<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, boost::property_tree::basic_ptree<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > > > >; boost::multi_index::detail::sequenced_index_node<Super>::impl_pointer = boost::multi_index::detail::sequenced_index_node_impl<std::allocator<char> >*]’ without object  
     impl_pointer xi=x->impl();  
                   ~~~~^~

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-11-30 19:14:42 UTC  
> Url: https://github.com/boostorg/multi_index/issues/52#issuecomment-982939071  

Hi,  
  
Without a MCVE I'm afraid I can't provide much help. This certainly looks like a compiler bug. Things you could try:  
  
* Upgrade your NVCC compiler.  
* Use `-ccbin clang++` or go back to CUDA 10.0, as suggested [here](https://forums.developer.nvidia.com/t/cuda-10-1-nvidia-youre-now-fixing-gcc-bugs-that-gcc-doesnt-even-have/71063).  
* Use the workaround provided [here](https://stackoverflow.com/a/55657954/213114) (I suspect you're that thread's OP anyway).  
* Upgrade your Boost installation (I don't think this will do much, though).  
  
Best,

---

## Comment 2

> Username: joaquintides  
> Created at: 2021-12-08 17:01:24 UTC  
> Url: https://github.com/boostorg/multi_index/issues/52#issuecomment-988997257  

Closing due to lack of further feedback from OP.
