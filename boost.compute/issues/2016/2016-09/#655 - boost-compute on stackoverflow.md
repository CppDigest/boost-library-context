# #655 - boost-compute on stackoverflow [Closed]

> Username: Slonegg  
> Created at: 2016-09-09 23:09:11 UTC  
> Updated at: 2017-03-22 17:54:42 UTC  
> Closed at: 2017-03-22 17:54:42 UTC  
> Url: https://github.com/boostorg/compute/issues/655  

I've tried to ask the question about boost::compute on stackoverflow and noticed that there is not even a boost-compute tag there. Maybe it's worth to establish some support there.  
http://stackoverflow.com/questions/39402091/boostcompute-stream-compaction  
##

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-09-09 23:14:56 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246066052  

https://github.com/boostorg/compute/issues/646 should help you with the question you asked on stackoverflow.

---

## Comment 2

> Username: Slonegg  
> Created at: 2016-09-09 23:28:25 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246067834  

Thanks Jakub! Do you suggest to do exclusive scan then transform_if it? Or  
is there some lambda that returns index of the element?  
  
On Fri, Sep 9, 2016 at 4:14 PM, Jakub Szuppe notifications@github.com  
wrote:  
  
> #646 https://github.com/boostorg/compute/issues/646 should help you  
> with the question you asked on stackoverflow.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/compute/issues/655#issuecomment-246066052,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/ACSyAANRBSL20VurgPSCqMuwsNF0hNalks5qoehzgaJpZM4J5jho  
> .

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-09-09 23:44:28 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246069879  

`transform_if` does exactly what you described in your question. You just need to pass a predicate for selecting elements and a transform function.   
  
If you just want to copy elements that satisfy given predicate, use `copy_if` (you can check that it uses `transform_if`). If you have a mask already, use `transform_if` as I explained in linked issue.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-09-09 23:47:31 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246070230  

And if you just need the indices, look at the implementation of `transform_if`.

---

## Comment 5

> Username: Slonegg  
> Created at: 2016-09-09 23:57:09 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246071341  

Yes, I see that I can do compact with exclusive_scan + transform_if or  
exclusive_scan + custom kernel. But first is inefficient because  
transform_if does scan internally. The second one is inconvenient.  
  
I though that there should be some short and efficient way to do stream  
compaction with boost::compute, because this is one of the most popular  
operations. There is even compact_kernel in boots::compute::detail

---

## Comment 6

> Username: jszuppe  
> Created at: 2016-09-10 08:15:35 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246098952  

I don't really know why you need the first `exclusive_scan`, so maybe we just start from the beginning, and you describe what are the inputs and what result you expect. (Sorry that I couldn't  answer earlier, it was 2 am in Poland).

---

## Comment 7

> Username: Slonegg  
> Created at: 2016-09-10 16:53:13 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246122423  

Hi Jakub, no worries at all, thanks for your help! I'm trying to do a variant of stream compaction (http://http.developer.nvidia.com/GPUGems3/gpugems3_ch39.html). Given vector `mask = [1 0 0 1 0 1 0 1]` I want to find indices of elements where `mask[i] = 1`, e.g. `inds = [0 3 5 7]`. With raw opencl I usually compute exclusive scan of the mask vector `scan = [0 1 1 1 2 2 3 3]`, then launch the following kernel: `if (mask[i]) inds[scan[i]] = i;` size of the `inds` array is then `mask.back() + scan.back()`.  
  
I was using this operation literally everywhere: marching cubes, random forest, kinect fusion, etc. Typical use case is when you have heavy kernel which you need to run only on certain elements of the large array. For marching cubes these are blocks with isosurface intersection, which occupy only few percent of the whole volume. Same thing for kinect fusion. For decision tree construction I've used compact to permute samples after split.  
  
It seems to me that sophisticated algorithm you will still need implement by hand, by boost::compute can help with intermediate steps, like computing scan, sorting, doing stream compaction.

---

## Comment 8

> Username: jszuppe  
> Created at: 2016-09-10 17:38:06 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246124876  

OK.  
  
The easiest way, use copy_index_if:  
  
``` cpp  
boost::compute::copy_index_if(  
      boost::compute::make_zip_iterator(  
          boost::compute::boost::make_tuple(  
              data_begin,  
              mask_begin  
          )  
      ),  
      boost::compute::make_zip_iterator(  
          boost::compute::boost::make_tuple(  
              data_end,  
              mask_end  
          )  
      ),  
     output_begin,  
     boost::compute::lambda::get<1>(boost::compute::_1) == 1, // lambda function that checks if mask is 1  
     queue // command queue (boost::compute::command_queue object)  
 );  
```  
  
More complicated way, but faster, write your own version `transform_if` (my code may contains some minor bugs since I wrote it here...):  
  
``` cpp  
  
template<class MaskIterator, class OutputIterator>  
inline OutputIterator get_indices(MaskIterator first,  
                                                     MaskIterator last,  
                                                     OutputIterator result,  
                                                     command_queue &queue)  
{  
    typedef typename std::iterator_traits<OutputIterator>::difference_type difference_type;  
  
    size_t count = detail::iterator_range_size(first, last);  
    if(count == 0){  
        return result;  
    }  
  
    ::boost::compute::vector<cl_uint> indices(count, context);  
  
    // scan indices  
    ::boost::compute::exclusive_scan(  
        first, last, indices.begin(), queue  
    );  
  
    size_t copied_element_count = (indices.end() - 1).read(queue) + (last - 1).read(queue);  
  
    // copy values  
    ::boost::compute::detail::meta_kernel k2("masked_copy");  
    k2 << "if(" << first[k2.get_global_id(0)] << " == 1)" <<  
          "    " << result[indices.begin()[k2.get_global_id(0)]] << " = "  
                  << k2.get_global_id(0) << ";\n";  
  
    k2.exec_1d(queue, 0, count);  
    return result + static_cast<difference_type>(copied_element_count);  
}  
  
```  
  
Ofc, if you use the same mask multiple times you should take the scan outside of the function to save some time.

---

## Comment 9

> Username: Slonegg  
> Created at: 2016-09-10 21:07:33 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246140447  

Thanks Jacob, seems to work!  
  
```  
copy_index_if(mask.begin(), mask.end(), inds.begin(), _1 == 1, queue);  
```

---

## Comment 10

> Username: jszuppe  
> Created at: 2016-09-10 21:11:01 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246140607  

Oh, yes, `zip_iterator` was unnecessary.  I'm glad it works.

---

## Comment 11

> Username: Slonegg  
> Created at: 2016-09-12 17:47:11 UTC  
> Url: https://github.com/boostorg/compute/issues/655#issuecomment-246431141  

We've created a tag boost-compute, will be available after review http://stackoverflow.com/tags/boost-compute/info
