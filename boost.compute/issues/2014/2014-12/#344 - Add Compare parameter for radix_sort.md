# #344 - Add Compare parameter for radix_sort [Open]

> Username: pavanky  
> Created at: 2014-12-15 19:00:46 UTC  
> Updated at: 2017-03-21 04:14:43 UTC  
> Url: https://github.com/boostorg/compute/issues/344  

This may not be a straight forward thing to do. But the possibility of adding this functionality should be discussed.  
##

---

## Comment 1

> Username: ddemidov  
> Created at: 2014-12-15 19:14:15 UTC  
> Url: https://github.com/boostorg/compute/issues/344#issuecomment-67047923  

There was recently a [discussion](http://lists.boost.org/boost-announce/2014/11/0421.php) on Boost mailing lists regarding review of [Spreadsort](https://github.com/spreadsort/sort) library by Steven Ross (@spreadsort). Spreadsort combines radix sort and bucket sort and Steven [claims](http://lists.boost.org/Archives/boost/2014/12/218260.php) that non-trivial element types may be easily adopted to be sortable with this algorithm. May be those ideas could be used for Boost.compute as well?

---

## Comment 2

> Username: spreadsort  
> Created at: 2014-12-16 01:13:38 UTC  
> Url: https://github.com/boostorg/compute/issues/344#issuecomment-67097089  

"Easily" is a stretch, but comparisons can be systematically converted into radix-based operations with about 5X the code that usually run faster than comparison-sorting even on conventional CPU systems without using explicit SIMD operations.  boost::sort::string_sort takes a get_char functor (which gets a byte at a specified offset), and a length.  Any comparison can be encoded using this pair of functions, though it's not always trivial.  What exactly is your goal?

---

## Comment 3

> Username: ddemidov  
> Created at: 2014-12-16 03:51:28 UTC  
> Url: https://github.com/boostorg/compute/issues/344#issuecomment-67108920  

@spreadsort, Boost.compute normally uses parallel radixsort to sort things on GPU, but falls back to serial insertion sort in case there is a custom comparison functor (see #343). I thought that radixsort could use a function similar to your `get_char()` in spreadsort. But I guess the task of creating this function automatically based only on a generic comparison functor supplied by the user is not easily solvable, and the user would have to supply this function instead.

---

## Comment 4

> Username: spreadsort  
> Created at: 2014-12-18 11:29:10 UTC  
> Url: https://github.com/boostorg/compute/issues/344#issuecomment-67473828  

A human can convert a compare function to a get_char and get_length functions systematically.  Here's how:  
Take each actual word comparison operation that occurs in the compare function and , and return the equivalent value used in the comparison from the get_char function at the equivalent index.  You could automatically convert some user-defined compare functions, but probably not all (some would probably need to be manual).  
  
For example, when sorting strings that aren't at the end of the key (at the end of the key you can simplify), this chunk of code in the compare function:  
if (x.first_name != y.first_name) {  // Not a comparison, a speed optimization, you'd need to detect that.  
      return x.first_name < y.first_name;  // Here's the real comparison  
}  
  
which consists of these equivalent word comparisons operations:  
for (int index = 0; true; ++index) {  
  if((index < x.first_name.size()) != (index < y.first_name.size()))  
    return (index < x.first_name.size()) < (index < y.first_name.size());  //You need this byte in your radix  
  if (index == x.first_name.size())  // Time to move on to the next sub-key  
   break;  
  if (x.first_name[index] != y.first_name[index])  
    return x.first_name[index] < y.first_name[index]; //You need this byte in your radix  
}  
The tricky part is that the string length checking is effectively doing a second comparison at every byte index, and you need to include that in the radix, or if you know the strings can't use all possible bits, squeeze an end-of-string code into the character set.   
  
(note: first_name_offset is the offset to the current byte; offset is the current byte offset being returned for comparison)  
converts to:  
    const unsigned first_name_end_offset = first_name_offset + x.first_name.size() \* 2;  
    if (offset < first_name_end_offset) {  
      int char_offset = offset - first_name_offset;  
      // This signals that the string continues.  
      if (!(char_offset & 1)) {  
        return 1;  
      }  
      return x.first_name[char_offset >> 1];  
    }  
    // This signals that the string has ended, so that shorter strings come  
    // before longer ones.  
    if (offset == first_name_end_offset) {  
      return 0;  
    }  
  
You can see my full (operational if you download the library) generalized multi-key sorting example here: https://github.com/spreadsort/sort/blob/master/example/generalizedstruct.cpp
