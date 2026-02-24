# #47 - New axis: piecewise_regular [Open]

> Username: HDembinski  
> Created at: 2018-06-19 07:19:58 UTC  
> Updated at: 2023-06-07 18:56:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/47  

A composed axis of several connected regular axis without gaps. Should use regular_axis underneath.

---

## Comment 1

> Username: swornim1  
> Created at: 2019-04-02 21:48:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/47#issuecomment-479218650  

I would like to work on this project. Please explain the further details required to accomplish the task. Thank you.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-04-03 08:59:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/47#issuecomment-479402609  

Hi,  
  
thank you for your interest.  
  
you should look into the implementation of boost::histogram::axis::regular to get an idea how this axis would work, also have a look at the Axis concept in the documentation.  
  
I gave this axis more thought in the meantime and it should be even more generalized: piecewise is a new axis which pieces several other axis types together.  
  
```  
template <class... Ts>  
class piecewise { ... };  
```  
  
The `Ts...` are other axis types.  
  
It is created by  a `make_piecewise` factory function.  
  
```  
template <class... Ts>  
piecewise<Ts...> make_piecewise(Ts... ts) {  
    ...  
}  
```  
  
Upon construction, `piecewise` checks that the boundaries of its axis arguments are connected, meaning that the uppermost bin edge of the first axis is equal to the lowermost bin edge of the second axis, etc. It keeps a std::tuple of the axis types. When a value is passed to the index method, it looks the sub-axis up whose range covers the argument value and passes the value to index method of that sub-axis. The index returned is shifted by the number of bins left of that sub-axis.

---

## Comment 3

> Username: ryanelandt  
> Created at: 2023-06-06 22:01:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/47#issuecomment-1579506408  

Are you looking for a new axis that pieces together:  
1. truly arbitrary axis types,  
2. arbitrary regular axis types, or   
3. arbitrary regular and variable axis types?  
In other words:  
```C++  
make_piecewise(axis::category, axis::boolean, axis::regular)           // 1  
make_piecewise(axis::regular<sqrt>, axis:regular, axis::regular<log>)  // 2  
make_piecewise(axis::variable, axis::regular)                          // 3  
```  
If it's the first one, what is the use case?

---

## Comment 4

> Username: HDembinski  
> Created at: 2023-06-07 07:50:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/47#issuecomment-1580131236  

I was originally thinking only of 2) but 3) is a natural generalization. As a further generalization, it would be great to also stitch integer axes together, but then the code needs to ensure that integer axes cannot be mixed with regular or variable axes. This can be achieved with a static_assert on the value_type of the axes, they either need to be all integer or all floating point. `make_piecewise` should check axis::trait::continuous and reject axes that are not continuous.  
  
We cannot do 1). It only makes sense to stitch continuous unbounded axes together, so we don't need to worry about stitching axis::category or axis::boolean.

---

## Comment 5

> Username: ryanelandt  
> Created at: 2023-06-07 14:28:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/47#issuecomment-1580944313  

I can see how piecing together floating axis types might give spacing that is difficult to achieve with a simple transformation (e.g., sqrt, log, etc). But, I'm not seeing why one would want to stitch together integer axes. If I understand it correctly, the width of an integer axis is always one. Is there a potential advantage?

---

## Comment 6

> Username: HDembinski  
> Created at: 2023-06-07 18:56:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/47#issuecomment-1581349637  

> But, I'm not seeing why one would want to stitch together integer axes.  
  
True, it makes no sense if we do not allow gaps between consecutive axes. I had not read the whole issue again to refresh my memory, so I was considering to allow gaps between axes. In that case it might be useful to stitch several integer axes together with gaps between them.  
  
You could use a category<int> axis to have gaps in an integer range, but bin lookup for a category<int> axis is more expensive and you need to allocate memory from the heap for a category axis, as opposed to an integer axis.
