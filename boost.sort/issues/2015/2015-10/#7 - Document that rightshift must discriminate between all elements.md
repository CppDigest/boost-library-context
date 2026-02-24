# #7 - Document that rightshift must discriminate between all elements [Closed]

> Username: mglisse  
> Created at: 2015-10-09 21:44:37 UTC  
> Updated at: 2016-04-19 02:55:55 UTC  
> Closed at: 2016-04-19 02:55:55 UTC  
> Url: https://github.com/boostorg/sort/issues/7  

Hello,  
  
from the documentation, it isn't clear to me whether I am allowed to float_sort a container of `pair<float,void*>` using a comparator that compares the first member and in case of equality disambiguates by complicated operations on the pointer, and a rightshift that only considers the float. In this case, rightshift does not provide the whole information, and the algorithm should finish by sorting elements with all identical rightshift using the comparator.  
  
I convinced myself that it wasn't supported using the following program on this file (on x86_64-linux-gnu):  
http://geometrica.saclay.inria.fr/team/Marc.Glisse/tmp/test.txt.gz  
  
```  
#include <boost/sort/sort.hpp>  
#include <fstream>  
#include <algorithm>  
#include <cassert>  
  
int main(){  
  std::ifstream file ("test.txt");  
  typedef std::pair<float,int> P;  
  std::vector<P> v;  
  struct C {  
    bool operator()(P const&a, P const&b)const{  
      if(a.first!=b.first){  
        assert(a.first<b.first == a.second<b.second);  
      }  
      return a.second<b.second;  
    }  
  };  
  float f; int i;  
  while (file >> f >> i) v.emplace_back(f,i);  
  boost::sort::spreadsort::float_sort(v.begin(),v.end(),  
      [](P const&x, unsigned offset){ return boost::sort::spreadsort::float_mem_cast<float,int>(x.first)>>offset; },  
      C());  
  assert(std::is_sorted(v.begin(),v.end(),C()));  
}  
```

---

## Comment 1

> Username: spreadsort  
> Created at: 2015-10-10 00:57:33 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-147018502  

On Fri, Oct 9, 2015 at 5:44 PM, Marc Glisse notifications@github.com  
wrote:  
  
> Hello,  
>   
> from the documentation, it isn't clear to me whether I am allowed to  
> float_sort a container of pair<float,void*> using a comparator that  
> compares the first member and in case of equality disambiguates by  
> complicated operations on the pointer, and a rightshift that only considers  
> the float. In this case, rightshift does not provide the whole information,  
> and the algorithm should finish by sorting elements with all identical  
> rightshift using the comparator.  
>   
> float_sort only works with float keys; if you can point me to a place in  
> the documentation where it says otherwise, I'll be happy to fix it.  
  
If you want a general hybrid-radix sort that can sort floats and multiple  
data fields efficiently, you'll need to look at stringsort.  Specifically,  
I recommend looking at  
http://www.boost.org/doc/libs/1_59_0/libs/sort/example/generalizedstruct.cpp.  
It's cumbersome to implement the bracket functor, but stringsort is faster  
than std::sort on complex keys in my testing.

---

## Comment 2

> Username: mglisse  
> Created at: 2015-10-10 08:16:31 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-147059838  

Thanks for your answer.  
  
> float_sort only works with float keys; if you can point me to a place in the documentation where it says otherwise, I'll be happy to fix it.  
  
It doesn't say otherwise, but maybe it could state it more strongly. I read `rshift(a,n)<rshift(b,n) -> compare(a,b)` but not the reverse implication (for a specific n).  
As far as the radix-sort part of the algorithm is concerned, I am sorting floats. The interface of float_sort is confusingly generic. Why take 2 complicated functors rightshift and compare instead of just one that simply gives access to the float/double? Then I would have had no doubt.  
  
> If you want a general hybrid-radix sort that can sort floats and multiple data fields efficiently, you'll need to look at stringsort.  
  
From the doc, it doesn't seem to provide more guarantees than float_sort for my case. That is, I would like to use radix-sort to do the heavy lifting, but let a regular comparison-based sort finish the job on elements that radix-sort thinks are equivalent (or on larger sets if spreadsort thinks it will be faster, which from the doc seems to happen at least part of the time). Except for the float, my comparison predicate does not have an equivalent formulation using brackets: it compares two elements by walking in a graph from one node trying to find the other one. In the extreme case where all floats are equal, I would want the algorithm to degenerate to a call to std::sort on the whole data (actually, as a heuristic, I would prefer stable_sort as that tends to speed up the following operations in my program, but that's not so important).  
  
I'll see if I can use a slightly different order that can fit string_sort, but it will be expensive (getsize is not well suited to something like a forward_list, where the first few elements might be enough for sorting) and cumbersome as you say. Did you consider making a helper float_bracket corresponding to the block "Sort a signed float" in generalizedstruct.cpp so we don't have to copy-paste it everywhere? integer_bracket would also be good.

---

## Comment 3

> Username: spreadsort  
> Created at: 2015-10-12 01:17:37 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-147269193  

Marc,  
  
On Sat, Oct 10, 2015 at 4:16 AM Marc Glisse notifications@github.com  
wrote:  
  
> Thanks for your answer.  
>   
> float_sort only works with float keys; if you can point me to a place in  
> the documentation where it says otherwise, I'll be happy to fix it.  
>   
> It doesn't say otherwise, but maybe it could state it more strongly. I  
> read rshift(a,n)<rshift(b,n) -> compare(a,b) but not the reverse  
> implication (for a specific n).  
>   
> Done.  compare(a, b) -> rshift(a, 0) < rshift(b, 0), aside from negative  
> floats (which are in reverse order as ints).  I've pushed this doc change  
> to develop.  
>   
> As far as the radix-sort part of the algorithm is concerned, I am sorting  
> floats. The interface of float_sort is confusingly generic. Why take 2  
> complicated functors rightshift and compare instead of just one that simply  
> gives access to the float/double? Then I would have had no doubt.  
>   
> The rshift operator is there in case >> does something special in the class  
> (like print it to a stream) can't be changed to be used for bitshift.  It  
> would be inefficient to convert two numbers from float to int (a banned  
> conversion, hence requiring an intermediate data copy) simply for the  
> purpose of comparing them.  
>   
> If you want a general hybrid-radix sort that can sort floats and multiple  
> data fields efficiently, you'll need to look at stringsort.  
>   
> From the doc, it doesn't seem to provide more guarantees than float_sort  
> for my case. That is, I would like to use radix-sort to do the heavy  
> lifting, but let a regular comparison-based sort finish the job on elements  
> that radix-sort thinks are equivalent (or on larger sets if spreadsort  
> thinks it will be faster, which from the doc seems to happen at least part  
> of the time). Except for the float, my comparison predicate does not have  
> an equivalent formulation using brackets: it compares two elements by  
> walking in a graph from one node trying to find the other one. In the  
> extreme case where all floats are equal, I would want the algorithm to  
> degenerate to a call to std::sort on the whole data (actually, as a  
> heuristic, I would prefer stable_sort as that tends to speed up the  
> following operations in my program, but that's not so important).  
>   
> Can you define a strict weak ordering?  Given that, I can define your  
> bracket operator.  It might not be efficient, but you can always convert  
> from a correct comparison operation to a radix operation.  
  
Depending on your situation (and how much code you really want to use on  
sorting), you might want to just use float_sort to sort everything with a  
different float value, followed by subsorting every clump with identical  
floats using std::sort.  The extra runtime overhead of this approach vs  
reimplementing float_sort to do what you want is trivial.  Alternatively,  
you can just call std::sort on the whole thing after float_sort completes.  
  
Additionally, if you really want stable sorting, you can modify any of the  
spreadsort implementations to use a second array and be stable, eliminating  
all the crazy swapping it does, and use a stable secondary sort.  The net  
result is simpler code that is comparably fast, but uses an extra copy of  
the input data.  The expense of this copy can be reduced by sorting  
pointers instead of the original data, though that will likely impact  
runtime due to the extra indirection.  
  
Note: The float_sort in 1.59.0 has a bug that I've fixed in master.  Please  
use the version in master: https://github.com/boostorg/sort  
integer_sort and string_sort are fine.  
  
> I'll see if I can use a slightly different order that can fit string_sort,  
> but it will be expensive (getsize is not well suited to something like a  
> forward_list, where the first few elements might be enough for sorting) and  
> cumbersome as you say. Did you consider making a helper float_bracket  
> corresponding to the block "Sort a signed float" in generalizedstruct.cpp  
> so we don't have to copy-paste it everywhere? integer_bracket would also be  
> good.  
>   
> I am curious now, what is the application?  I haven't seen a modern  
> application where the CPU usage was expensive (more than a software  
> engineer's salary), and sorting accounted for more than ~20% of the total  
> CPU.  If you have one, I'd be quite interested to hear more.

---

## Comment 4

> Username: mglisse  
> Created at: 2015-10-12 12:18:20 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-147379365  

> Done.  
  
Thanks.  
  
> The rshift operator is there in case >> does something special in the class (like print it to a stream) can't be changed to be used for bitshift. It would be inefficient to convert two numbers from float to int (a banned conversion, hence requiring an intermediate data copy) simply for the purpose of comparing them.  
  
There seems to be a misunderstanding. I meant having a functor accessor such that accessor(data) gives a float/double, and then internally you can compare those floats or cast/shift them, etc. If floats are all you can work with, it is surprising that floats don't really appear in the interface.  
  
```  
template<class Iterator, class Accessor> void my_float_sort(Iterator a, Iterator b, Accessor accessor) {  
  float_sort(a, b,  
    [](auto const&x, unsigned offset){ return float_mem_cast<some_type_from_boost_integer>(accessor(x)) >> offset; },  
    [](auto const&x, auto const&y){return accessor(x)<accessor(y);});  
}  
```  
  
From a user point of view, I can use this without having to understand float_mem_cast and shifting, and I am not sure I am loosing much generality by using this interface.  
  
> Can you define a strict weak ordering? Given that, I can define your bracket operator. It might not be efficient, but you can always convert from a correct comparison operation to a radix operation.  
  
I just checked, there were outdated comments in the code, and we are actually using a total ordering now. The reason we are still calling std::stable_sort instead of std::sort is simply that with libstdc++ and on our data, it is faster (by more than 10% on the last example I tried). In case it matters, we are sorting a vector of pointers, with a relatively costly comparison (random memory accesses). This new strict ordering would actually not be that hard to adapt to string_sort (although getsize may end up costing a lot if its result is not cached), I'll try it when I get a chance.  
  
> Depending on your situation (and how much code you really want to use on sorting), you might want to just use float_sort to sort everything with a different float value, followed by subsorting every clump with identical floats using std::sort. The extra runtime overhead of this approach vs reimplementing float_sort to do what you want is trivial. Alternatively, you can just call std::sort on the whole thing after float_sort completes.  
  
I was considering that indeed.  
  
> I am curious now, what is the application?  
  
We are computing persistent cohomology of a filtered simplicial complex ;-)  
  
If you are not familiar with it, a filtered simplicial complex is a hypergraph with an order on the hyperedges, and computing persistent (co)homology is some kind of Gauss reduction on the adjacency matrix, that could in theory take cubic time but in practice is quasi-linear. When we do this on the Rips filtration (one particular complex built from a point cloud), building the complex (even naively) takes little time, computing persistence takes maybe 55% of the time, and surprisingly sorting the simplices takes about 1/3 of the total running time (this is just one example, things may vary a lot). Hence my interest in some faster sorting :-)  
  
Note that the CPU is not the limiting factor, it is the memory accesses. Most of the sorting time is spent loading the number from memory, and most of the persistence time is spent looking up the boundary of a simplex.

---

## Comment 5

> Username: spreadsort  
> Created at: 2015-10-13 13:46:48 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-147719683  

On Mon, Oct 12, 2015 at 8:18 AM Marc Glisse notifications@github.com  
wrote:  
  
> The rshift operator is there in case >> does something special in the  
> class (like print it to a stream) can't be changed to be used for bitshift.  
> It would be inefficient to convert two numbers from float to int (a banned  
> conversion, hence requiring an intermediate data copy) simply for the  
> purpose of comparing them.  
>   
> There seems to be a misunderstanding. I meant having a functor accessor  
> such that accessor(data) gives a float/double, and then internally you can  
> compare those floats or cast/shift them, etc. If floats are all you can  
> work with, it is surprising that floats don't really appear in the  
> interface.  
>   
> In that case it could only accept a float and a double, and would have to  
> specialize the code for just those cases.  In case somebody has a different  
> IEEE float data type (half, quadruple, decimal), they wouldn't be able to  
> use it.  I agree, for the standard cases, that would be easier to use, but  
> I want to maximize flexibility without impairing performance.  
>   
> template<class Iterator, class Accessor> void my_float_sort(Iterator a, Iterator b, Accessor accessor) {  
>   float_sort(a, b,  
>     [](auto const&x, unsigned offset){ return float_mem_cast<some_type_from_boost_integer>(accessor(x)) >> offset; },  
>     [](auto const&x, auto const&y){return accessor(x)<accessor(y);});  
> }  
>   
> From a user point of view, I can use this without having to understand  
> float_mem_cast and shifting, and I am not sure I am loosing much generality  
> by using this interface.  
>   
> What should I call it if I add this wrapper?  
>   
> Can you define a strict weak ordering? Given that, I can define your  
> bracket operator. It might not be efficient, but you can always convert  
> from a correct comparison operation to a radix operation.  
>   
> I just checked, there were outdated comments in the code, and we are  
> actually using a total ordering now. The reason we are still calling  
> std::stable_sort instead of std::sort is simply that with libstdc++ and on  
> our data, it is faster (by more than 10% on the last example I tried). In  
> case it matters, we are sorting a vector of pointers, with a relatively  
> costly comparison (random memory accesses). This new strict ordering would  
> actually not be that hard to adapt to string_sort (although getsize may end  
> up costing a lot if its result is not cached), I'll try it when I get a  
> chance.  
>   
> In that case, the code to find whether the data is already sorted and the  
> maximum and minimum is probably hurting the performance.  I think you'd get  
> optimal performance by ripping that out, and ripping out the swap loop and  
> instead having two copies of your vector of pointers that it moves pointers  
> back and forth between.  I suspect you could cut the runtime of your sort  
> by a third or so relative to what you get using plain float_sort and doing  
> a final comparison-based sort if you specialized it in this fashion.  
> Do you often get > 1000 values with the same float key?  
  
I am curious now, what is the application?  
  
> We are computing persistent cohomology of a filtered simplicial complex ;-)  
>   
> Note that the CPU is not the limiting factor, it is the memory accesses.  
> Most of the sorting time is spent loading the number from memory, and most  
> of the persistence time is spent looking up the boundary of a simplex.  
>   
> Can you run this in parallel over multiple machines?  Can you sort with  
> parallel threads?  It sounds like it could be an expensive computation if  
> you have enough data.  We're working on adding a parallel sort to the  
> library, and 8 cores could bring sorting time down enough that you don't  
> really care about the float_sort speedup.  Of course, there is no reason in  
> theory you couldn't sort in parallel with float_sort as the approach used  
> to sort on each child thread.

---

## Comment 6

> Username: mglisse  
> Created at: 2015-10-13 14:38:38 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-147734908  

> In that case it could only accept a float and a double, and would have to specialize the code for just those cases. In case somebody has a different IEEE float data type (half, quadruple, decimal), they wouldn't be able to use it. I agree, for the standard cases, that would be easier to use, but I want to maximize flexibility without impairing performance.  
  
I disagree that it would be limited to float/double. It would take a template type, maybe do some sanity checks with numeric_limits (though gcc doesn't seem to handle half-floats or `__float128` there), find a suitable integer type with sizeof and Boost.Integer, I would expect half and quadruple to just work. I am not that familiar with decimals, but they seem similar enough to just work as well. The x87 80-bit type may be harder...  
Even if we want more generality, I am not sure why `>> offset` needs to be in the user's code, the user could also provide a functor to do the integer cast and float_sort could manage the shift itself.  
For `__float128` I could be tempted to get 2 64-bit integers instead of an `__int128` and return some appropriate int64_t depending on the offset, but the code to handle negative numbers would make that pretty hard and I'd probably have to use string_sort instead.  
  
> What should I call it if I add this wrapper?  
  
No idea. I am mostly trying to understand the design and how generic / specific the function is, not really proposing anything. Please don't feel forced to change the interface because of my comments.  
  
> In that case, the code to find whether the data is already sorted and the maximum and minimum is probably hurting the performance. I think you'd get optimal performance by ripping that out, and ripping out the swap loop and instead having two copies of your vector of pointers that it moves pointers back and forth between. I suspect you could cut the runtime of your sort by a third or so relative to what you get using plain float_sort and doing a final comparison-based sort if you specialized it in this fashion.  
  
That seems interesting, but maintaining my own version of sort would be too much for this project. Using a non-standard version from boost is about the limit of what is reasonable.  
  
> Do you often get > 1000 values with the same float key?  
  
Depends. For some applications, very few values will have the same float (possibly none). For other applications, all values will have the same float (if I know it at compile-time, I can special-case it), or just many.  
  
> Can you run this in parallel over multiple machines?  Can you sort with  
> parallel threads?  It sounds like it could be an expensive computation if  
> you have enough data.  We're working on adding a parallel sort to the  
> library, and 8 cores could bring sorting time down enough that you don't  
> really care about the float_sort speedup.  Of course, there is no reason in  
> theory you couldn't sort in parallel with float_sort as the approach used  
> to sort on each child thread.  
  
Hmm, yes, multiple threads would make sense. I guess I was trying to see how fast we could go with a single thread, since we don't know how to parallelize the main part of the algorithm, but that's not a good reason. I'll experiment with libstdc++ parallel sort when I get a chance.

---

## Comment 7

> Username: Morwenn  
> Created at: 2015-10-13 15:28:44 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-147750615  

If you want raw speed in a single thread, you can trivially make `spreadsort` fall back to [`pdqsort`](https://github.com/orlp/pdqsort) instead of `std::sort`. I benchmarked spreadsort for integers and floating point numbers with `pdqsort` and `std::sort`. Basically, `pdqsort` almost always makes spreadsort a bit faster and it dramatically improves the performance in some cases (sequence sorted in reverse order for example). Note that `pdqsort` may be used to implement `std::sort` at some point in the future.

---

## Comment 8

> Username: mglisse  
> Created at: 2015-11-14 18:18:09 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-156730315  

I did eventually implement the functors for string_sort. However, the result is not very convincing, only a few % faster than std::stable_sort for code that is much more complicated. Making getsize return a fixed upper-bound (and having bracket return 0 if too far) helped a bit, since the tight getsize was expensive. Parallel sorting is the way I am going. Intel TBB gives a nice speed boost with a few threads (though surprisingly giving it many (100+) cores does not help). I might give boost.sort another try once you guys release a parallel version :-)  
  
Thanks for the whole discussion.

---

## Comment 9

> Username: spreadsort  
> Created at: 2015-11-17 02:43:58 UTC  
> Url: https://github.com/boostorg/sort/issues/7#issuecomment-157250905  

On Sat, Nov 14, 2015 at 1:18 PM Marc Glisse notifications@github.com  
wrote:  
  
> I did eventually implement the functors for string_sort. However, the  
> result is not very convincing, only a few % faster than std::stable_sort  
> for code that is much more complicated. Making getsize return a fixed  
> upper-bound (and having bracket return 0 if too far) helped a bit, since  
> the tight getsize was expensive. Parallel sorting is the way I am going.  
> Intel TBB gives a nice speed boost with a few threads (though surprisingly  
> giving it many (100+) cores does not help). I might give boost.sort another  
> try once you guys release a parallel version :-)  
>   
> You might want to look at this if you want to use more cores:  
> https://software.intel.com/en-us/articles/a-parallel-stable-sort-using-c11-for-tbb-cilk-plus-and-openmp  
  
Francisco is working on a fully-open-source competitor to that to include  
in Boost.Sort.  
  
> Thanks for the whole discussion.  
>   
> You're welcome.  A specialized sort can usually speed up about 20% above  
> what Spreadsort can do, but it's even more work; it sounds like your  
> application doesn't justify that level of effort at this point, especially  
> as parallelization is an option.
