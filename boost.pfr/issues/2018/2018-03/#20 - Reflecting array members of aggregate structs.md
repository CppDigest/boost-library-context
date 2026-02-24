# #20 - Reflecting array members of aggregate structs [Open]

> Username: willwray  
> Created at: 2018-03-12 21:52:43 UTC  
> Updated at: 2024-10-17 18:41:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/20  

Reflection of member array types is possible by refining magic_get methods.  
  
## Recursive scheme  
Here's a scheme for finding an aggregate's member types one at a time:  
   
Given an aggregate-initializable struct `S`:  
1. Assume initial member types `T...` of `S` are known (none to start)  
     => `S` can be aggregate initialized as `S { T_init... }`  
     with `T_init` braced or unbraced initialization as needed by `T`  
     (scalars need unbraced init, arrays need braced init => awkward).  
2. Attempt aggregate initialization `S { T_init..., ubiq_init }`  
     where `ubiq` is the usual convert-to-anything type.  
     If this fails then `T...` is the complete list of members: **Done**.  
3. Else redo initialization (2) but capture the ubiq converted-to type `L`  
    (either by 'Great Type Loophole' or by typeid memo).  
     If the member is array type then `L` is its base type  
    (on gcc ubiq actually sees the full array type - a tempting anomaly)  
4. Introspect the rank of the member base type L found in (3):  
     Attempt initializations of `S { T_init..., {L_init} }`  
     with increasing depth of nested braces => deduce the rank   
5. For non-zero rank, i.e. array types, find the array bounds:  
       e.g. for an array of rank 2 attempt initializations  
                `S { T_init..., {{Li...}} }`  
                `S { T_init..., {{Li}...} }`  
     with increasing numbers Li... => the maxes are the array bounds  
6. Append the new type to the known initiial member types `T...`  
7. Goto (1)  
  
- This scheme is not fully generic because of the need for both  
braced and unbraced init syntax in the comma-separated init list  
(variadic expansion of differing syntax in that context seems impossible).  
- Also, array rank deduction has an implementation-defined depth limit.  
  
## Array vs Scalar init  
Arrays and scalar types don't mix well in braced-init lists, requiring non-uniform initialization;  
to support both requires expanding both braced and unbraced initialization syntax.  
Again, given aggregate-initializable struct `S` with known initial member type sequence `T...`  
  
- Expanding unbraced `S{ T{}... }` fails for `T = L[N]` array type  
  - `array must be initialized with a brace-enclosed initializer`.  
- Expanding braced `S{ {T{}}... }` fails for scalar  
  - `error: braces around scalar initializer`  
  
A 'Catch 22' situation in generating a suitable init-list:  
  
### Workarounds for non-uniform initialization syntax  
For the initial member type initializations:  
- Separate `T...` into runs requiring braced or unbraced initialization:  
- Support pairs of unbraced runs U<sub>i</sub> and braced runs B<sub>i</sub> to some limit N:  
  - `T...` -> Ts<U<sub>0</sub>....>, Ts<B<sub>0</sub>....> ... Ts<U<sub>N-1</sub>...>, Ts<B<sub>N-1</sub>...>  
  
A different workaround would be to flatten all arrays and do all unbraced inits.  
For large arrays this explodes compile time and memory usage.  
Or, for a mixed workaround, fallback to flattening arrays only when necessary.  
If more than N pairs of runs are needed, selectively flatten smaller arrays  
in order to remove braced runs until there are only N pairs of runs.  
  
## Proof of Concept code  
I have code for this scheme, done in C++17 plus `-fconcepts` for ease  
(so currently limited to gcc, but not using gcc's direct extraction of the array type).  
It is prototype / experimental code that has not been much exercised yet.  
It is around 500 LOC including comments.  
It uses the Loophole ubiq.  
It expands up to 4 array dimensions.  
It expands up to 4 pairs of unbraced,braced runs  
(the array-flattening fallback has not been implemented so 4 is a hard limit).  
In principle, it should be possible to backport to C++14.  
I'm not motivated to backport or test with Clang / MSVC until they have concepts.  
Compile time for large arrays gets noticeable, likely the binary search for bounds.  
  
## Contribute to pfr / magic_get?  
I'm happy to provide the code in current form, to see if it is suitable for inclusion.  
There seem to be more cons than pros though...  
  
**Cons:**  
Arrays are awkward types so still need special-casing in generic client code.  
Don't want to encourage C-array usage - std::array is better if it can be used.  
Consumes more compile resource than current 'precise' and 'flat' methods.  
It comes burdened with implementation-defined limits.  
The code is involved so there's a maintenance cost.  
The backport is not trivial.  
  
**Pros:**  
Its the only way I know to reflect array members.  
Because it's there.  
  
## Note on structured bindings and 'structured reflection'  
Structured bindings get tantalisingly close to complete 'precise' struct reflection;   
if you know the member count in a struct then you can bind to and get all types.  
If you don't know then you have to count, but you can't directly count because  
you can't SFINAE on a structured binding failing, so you have to count indirectly.  
For an aggregate, this means counting via the init-list. We're back to init, innit.  
Egg-bound by the chickens.  
  
I floated this template-matching syntax for 'structured reflection';  
```  
template <class A> struct destructure;  
  
template <class A, auto... mp>  
struct destructure<A::[mp...]> {  
     using member_pointers = Members<mp...>;  
};  
  
struct S { bool b; char c[4]; };  
using S_members = typename destructure<S>::member_pointers;  
```  
If only it were this simple.

---

## Comment 1

> Username: glebushka98  
> Created at: 2018-05-22 14:01:25 UTC  
> Updated at: 2018-05-22 14:01:53 UTC  
> Url: https://github.com/boostorg/pfr/issues/20#issuecomment-391001393  

@willwray, I have some questions about your approach, may we discuss their (by email or here for example)?  
May you show me your c++17 implementation of this scheme?

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-10-17 09:02:12 UTC  
> Url: https://github.com/boostorg/pfr/issues/20#issuecomment-2418969915  

Please, provide a PR

---

## Comment 3

> Username: willwray  
> Created at: 2024-10-17 18:41:38 UTC  
> Url: https://github.com/boostorg/pfr/issues/20#issuecomment-2420279128  

Crikey!  
  
This should be a bit easier now...  
  
The 'Array vs Scalar init' issue turned out to be a compiler bug;  
the standard says that scalars should be brace-initializable too.  
I submitted the [gcc bug and patch](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=88572) (released in gcc 9),  
discussed the [clang bug](https://lists.llvm.org/pipermail/cfe-dev/2018-December/060598.html) (and Mr Smith promptly [fixed it](https://github.com/llvm-mirror/clang/commit/3410781dffff51e89751a2494bd8dfe7e7d83541) in clang 8).  
  
This means that braced-init really is 'uniform' which simplifies things.  
The tentative initializers can now consistently use braced-int.  
(I don't know the situation in MSVC.)  
  
The most straightforward approach will be via structured binding,  
assuming that members can be correctly counted now using braced init.  
  
I.e. hack `pfr/detail/fields_count.hpp` to count array members  
without breaking its many special-case workarounds  
then dispatch to `pfr/detail/core17_generated.hpp`.  
  
The 'init-list sniffing' approach is complicated by C array bounds deduction  
for large or possibly multidimensional arrays.
