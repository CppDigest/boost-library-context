# #250 - use arguments of storage to generate accumulator default value [Open]

> Username: HDembinski  
> Created at: 2019-11-11 16:53:26 UTC  
> Updated at: 2020-03-06 09:29:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/250  

The storage is passed as a value to the histogram, because it allows the storage to be configured at run-time.  
```  
auto h = make_histogram_with(dense_storage<int>(), ...);  
```  
  
So far we have not made use of this feature, but I propose to extend the `storage_adaptor` so that it accepts arguments in its ctor which are used to initialize the default value for its accumulator.  
```  
auto h = make_histogram_with(dense_storage<int>(3), ...);  
```  
Internally, the storage creates a value `int default_value{3}` with the arguments passed to it. Then, whenever the histogram resets the storage, the accumulators are initialized to this value.  
  
This feature allows one to pass run-time configuration to the accumulators, which is currently not possible.  
  
@henryiii @jpivarski any thoughts?

---

## Comment 1

> Username: henryiii  
> Created at: 2019-11-12 07:28:46 UTC  
> Updated at: 2019-11-12 07:36:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/250#issuecomment-552769770  

I can't come up with an obvious use case for this, so can't be much help here. This would fill flow bins with the same default, and uneven binning scheme with a single default too. A function that takes an indexed-like argument would be more useful (but rather heavyweight for this, and only saves two lines of code and a zero initialization, which is at least an order of magnitude faster on modern hardware than any functional/indexed iteration could be).  
  
The thing I'd like to see is a way to take an existing piece of memory (ideally supporting numpy-like strides and offsets) in and use that, ideally without a copy. This would be useful in converting numpy arrays and/or aghast flatbuffers (I think) into histograms without making a copy. In C++, this could be used to take eigen/XTensor (which I think have the same memory scheme), as well as could possibly be used to take over a ROOT histogram without a copy (probably not a weighted one, however, since I think the extra information is not stored adjacent like an accumulator expects). (Probably not related to changes on dense storage, but would be a runtime configuration)

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-11-12 10:52:39 UTC  
> Updated at: 2019-11-12 10:57:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/250#issuecomment-552841984  

It is open what the accumulator does with the initial arguments. For the currently builtin accumulators, this feature makes not much sense. But you could come up with some user-defined accumulator that needs some run-time configuration. Right now, you cannot pass this information to the accumulator. This feature is supposed to change this.  
  
Here is a use case. You want to store multiple weights, but you only know how many you need at run-time.  
  
```  
struct dynamic_weighted_sum {  
  dynamic_weighted_sum(unsigned nweights) : weights_(nweights, 0) {}  
  // ...  
  template <class Iterable>  
  void operator()(const Iterable& ws) {  
    // add ws to weights_ ...  
  }  
  
  std::vector<double> weights_;  
};  
```  
Usage:  
```  
// make histogram with 32 weights per cell  
auto h = make_histogram_with(dense_storage<dynamic_weighted_sum>(32), axis::integer(0, 2));  
  
std::vector<double> wv(32); // weight vector  
wv[0] = 1.2;  
// ...  
wv[32] = 5.6;  
  
// fill histogram  
h(0, weight(wv));  
```

---

## Comment 3

> Username: jbuonagurio  
> Created at: 2020-02-22 09:48:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/250#issuecomment-589938438  

I came across this in the process of creating an accumulator for rolling means with `window_size` defined at run-time. Basic implementation follows, which should handle unsigned value types.  
  
I could try to put together a PR. Any thoughts on the best way to extend `storage_adaptor` without breaking the forwarding constructor to underlying containers? Possibly create a tag type specifically for accumulator arguments?  
  
```cpp  
template <class ValueType>  
struct rolling_mean  
{  
    using value_type = ValueType;  
    using const_reference = const value_type&;  
    
    rolling_mean(std::size_t window_size) :  
        buffer_(boost::circular_buffer<value_type>(window_size))  
    {}  
      
    void operator()(const_reference x) {  
        if (buffer_.full()) {  
            if (buffer_.front() > x)  
                value_ -= (buffer_.front() - x) / buffer_.size();  
            else if (buffer_.front() < x)  
                value_ += (x - buffer_.front()) / buffer_.size();  
            buffer_.push_back(x);  
        }  
        else {  
            buffer_.push_back(x);  
            const auto prev_value = value_;  
            if (prev_value > x)  
                value_ -= (prev_value - x) / buffer_.size();  
            else if (prev_value < x)  
                value_ += (x - prev_value) / buffer_.size();  
              
        }  
    }  
      
    const_reference value() const noexcept { return value_; }  
      
private:  
    value_type value_{};  
    boost::circular_buffer<value_type> buffer_;  
};  
```

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-02-28 09:00:38 UTC  
> Updated at: 2020-02-28 09:04:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/250#issuecomment-592417131  

A PR for a rolling mean would be very appreciated, thanks! The implementation should not use circular_buffer though, to not add another dependency to boost::histogram. Enduser code should make of use boost facilities, but boost library code should avoid it if possible, since there are people want to use just boost-histogram and its dependencies and not all of boost.  
  
Also thanks for giving me one more reason to finally add this feature for the storage_adaptor.  
  
Regarding the forwarding, I was thinking of the following solution. We only forward an allocator to the storage itself. Anything else is passed to the accumulator. In the unlikely case that the accumulator needs an allocator, this would still work  
```  
storage_adaptor<...>(my_accumulator(my_allocator))  
```  
would be passed by this rule to the accumulator.
