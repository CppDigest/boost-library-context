# #78 - Possibility of excluded_range, excluded_literal [Closed]

> Username: kunaltyagi  
> Created at: 2020-01-21 22:43:18 UTC  
> Updated at: 2020-01-22 03:54:05 UTC  
> Closed at: 2020-01-22 02:23:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/78  

Is there a way to exclude certain ranges? `excluded_range<0, 0>` or `excluded_literal<0>`will never be zero: No worries about division anymore.  
  
I don't really have a use-case except for division by 0, but I can't have a one-sided range. `std::variant` doesn't help with this because the types evolve and the variant type needs to be modified all the time.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-01-21 23:51:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/78#issuecomment-576941462  

For questions like this I go to abstract algebra.  I've directly applied concepts from this branch of mathematics to implement this library.  The type checked<int> is an implementation of this.  So the question to me becomes - is there as set and operation that excludes zero?  the set of all integers other than zero might be closed under multiplication - but not addition.  Other operations might be possible but I'd have to think about it.   But the above does tell us that something like  
  
`  
template<typename T>  
struct non_zero {  
    T m_negatives;  
    T m_positives;  
}  
`  
is never going to fly as a "numeric type" because it can never support the concept of addition in an expected way.

---

## Comment 2

> Username: kunaltyagi  
> Created at: 2020-01-22 00:34:06 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/78#issuecomment-576952854  

> is there as set and operation that excludes zero?  
  
Not necessarily though. Since ranges in `safe_int` exclude a large part and still work as expected. The same mechanism used to propagate ranges can be used to propagate excluded items too.  
  
We can consider the ranges to be a special case specialization of a long variadic list, ie. `safe_int<-1, 1>` is equivalent to `safe_int_list<-1, 0, 1>`. For `safe_int`, any overload affects the beginning and the end, while for `safe_int_list` it affects every item. Then there might be an overload on `safe_int_list` such that either each item is a number or a range. This will allow the trivial case of exclusion of 0 by creating `safe_int_list<safe_int<-MIN, -1>, safe_int<1, MAX>>`. This seems doable.. sorta... but I don't know how much the penalty would be: compilation time, code complexity, bloat.  
  
Since I'm just spouting genie code, we can create another type which has a blacklist, and acts just like the whitelist type `safe_int_list` called `unsafe_int_list` which ensures that all items are propagated as usual, assignment rules check for overlap of allowed/disallowed regions: `safe_int_list<safe_int<-MIN, -1>, safe_int<1, MAX>>` has no item from `unsafe_int_list<0>`, so assignment is possible.  
  
Which one is better? Well, if I were to add 1 to both, it might overflow in `unsafe_int_list<0>` but we'll get `unsafe_int_list<1>` and `safe_int_list<safe_int<-MIN+1, 0>, safe_int<2, MAX+1>>`.  
  
Clearly, having exclusion zones like `unsafe_int_list` are worse for safety compared to a list of inclusion zones.  
  
I came here asking for exclusion zones, but now I feel like asking for a `safe_int_variant<safe_int...>`

---

## Comment 3

> Username: robertramey  
> Created at: 2020-01-22 02:23:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/78#issuecomment-576978121  

as I've said, I found it impossible to implement ideas not backed up by a good model phrased in terms of abstract algebra.  Off hand, I can't think of a set which excludes one special point can support an algebra - though it might if it only supports one operation.  
  
the following might be made to work.  
  
variant<safe_range<-2,-1>, safe<1, 2>>  
  
I don't know what that would entail.  Interesting thing to experiment with though

---

## Comment 4

> Username: kunaltyagi  
> Created at: 2020-01-22 03:52:44 UTC  
> Updated at: 2020-01-22 03:54:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/78#issuecomment-576995837  

> Off hand, I can't think of a set which excludes one special point can support an algebra  
  
The set excluding one special point will indeed not work. It fails to work with operations on itself.  
  
> the following might be made to work. I don't know what that would entail.  
  
It's essentially applying the same algebraic model on multiple ranges. I fail to see where the algebra will differ till there's a need to collapse the different variants to one choice.  
  
At that point, if the conversion is to a range such that all ranges of the variant are subset of the said range, the conversion is ok. Else the variant might be carrying some item not allowed in the output. This logic is similar to how assignment is carried out currently.  
  
This can be generalized for operations between 2 variants. For assignment/cast of variant `A` to variant `B`, the operation `B x = static_cast<A>(y);` is ok if `\cup_i ( \cap_j A_i B_j ) \neq \phi` where `A_i` and `B_j` are the individual ranges. For all other operations (division, multiplication, addition and subtraction), the usual rules apply to each range in the variant. The number of ranges in the resultant types will be the product of number of ranges in the operands. Of course, this number can be reduced in case of overlaps by checking `\cup_{i \ni S} A_i \mid \cap_{i \ni S} A_i \neq \phi`, but this might be problematic to implement in type system.   
  
A simple sample code for collapse of variant will look like:  
```c++  
safe_range<-4, 10> x = variant<safe_range<-3, 0>, safe_range<-2, 4>>(3);  // ok  
// since [-4, 10] \supseteq ([-3, 0] \cup [-2, 4])  
safe_range<-4, 3> nx = variant<safe_range<-3, 0>, safe_range<-2, 4>>(3);  // not ok  
// since [-4, 3] \nsupseteq ([-3, 0] \cup [-2, 4])  
```  
  
If there's a query function, that can be used to verify that a particular value is impossible to be achieved. Sample code:  
```c++  
// static assert for compile time check using type  
// function input parameter just to deduce the type  
static_assert(range_contains<0>(variant<safe_range<-3, 1>, safe_range<2, 4>>{3}));  
static_assert(!range_contains<0>(variant<safe_range<-3, -1>, safe_range<2, 4>>{3}));  
// using range_contains because that allows this same introspection to other types  
safe_int<-3, 4> x = 2;  
auto y = x + 1;  
auto x = z - 4;  
static_assert(range_contains<0>(z));  
static_assert(!range_contains<-7>(z));  
```
