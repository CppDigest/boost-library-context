# #274 Fix for Boost Trac tickets #10658, #10835 and #11121 [Merged]

> Username: mkaravel  
> Created at: 2015-03-26 07:06:44 UTC  
> Updated at: 2015-03-27 21:56:20 UTC  
> Merged at: 2015-03-27 21:56:20 UTC  
> Closed at: 2015-03-27 21:56:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/274  

This PR addresses Boost [Trac Ticket #10835](https://svn.boost.org/trac/boost/ticket/10835).  
  
The problem reported in the ticket was due to overflow in integer operations when the intersections points (for the second linear/areal difference) were computed.  
  
The way the problem is addressed in this PR is by promoting the integral coordinates to another integral type of larger size, for which overflow does not happen; the outcome of the arithmetic operations with the promoted integral type is then cast back to the original integral type.  
  
On a technical level, to perform this promotion, a new meta-function `promote_integral<>` has been implemented. This meta-function promotes a fundamental integral type `T` to a another integral type (possibly a multi-precision one) with size twice the size of `T`. At this point the support for promotion to a multi-precision integer is by default activated.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-26 12:11:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#issuecomment-86485977  

The fix proposed in this PR also fixes the issues reported in the following Boost Trac tickets:  
- [Trac Ticket #10658](https://svn.boost.org/trac/boost/ticket/10658)  
- [Trac Ticket #11121](https://svn.boost.org/trac/boost/ticket/11121)  
  
Test cases for these tickets have also been added. See commits 92d5132 and 37baa56.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-26 13:31:28 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27211909  

Wow, I haven't expected using MPL for this, but ok it's flexible.  
  
Why not "iterate" over the MPL sequence instead of "popping"? Wouldn't it take less time to compile?  
  
Is the `sizeof(boost::multiprecision::number<..., 2 * CHAR_BIT * sizeof(T), ...>)` always `>=` than `2*sizeof(T)`? Because if not, `bm::number<>` won't be picked.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-03-26 13:34:38 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27212139  

Shouldn't we promote unsigned types to bigger unsinged types? I know that they're not supported in general, but for consistency.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-26 14:21:40 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27216247  

- About "iterate": I am not sure I really gain on compile time when iterating over the elements of the sequence. I still have to instantiate templates equal to the number of elements in the list, so I do not see how I will gain from that... But you are right it is possible to do it this way as well.  
- About sizes: yes. Running the unit test in debug mode reveals that this is the case, at least on my platform.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-03-26 14:24:09 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27216496  

I asked my self about it too. It is not clear to me whether we should promote unsigned numbers to larger unsigned numbers or to larger signed numbers. It seemed to me that it is safer to promote to signed numbers.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-03-26 16:38:06 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27230450  

Going back to the promotion of unsigned types: I think the best way to address this issue is to leave it to the user of the `promote_integral<>` meta-function: my suggestion is to add a boolean template parameter `PromoteUnsignedToUnsigned`, which if set to `true` will promote unsigned types to unsigned, and unsigned types to signed types otherwise. I would prefer this template parameter to have a default value, with this default value being `false`.  
What do you think?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-03-26 17:35:46 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27236310  

Yes, this seems to be a good idea. In the case of ratios it'd be sufficient to use unsigned types but e.g. the result of determinant for unsigned input may be negative. So the kind of promotion should probably depend on the use case.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-03-26 17:41:47 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27236921  

Shouldn't call to `get()` be outside the outer `numeric_cast`? It may be not possible to add a value returned by this function to the object of promoted_type. So it should probably also be promoted explicitly or moved outside.  
  
If you care about the overflow, it doesn't change anything since the result is converted to the `coordinate_type` in `set()` so there would be an overflow anyway. Or would numeric_cast throw an exception? If yes, would it be desireable?

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-03-27 09:58:57 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27284879  

I re-implemented the `mpl::list` traversal using iterators (as suggested).  
See commit 7455682.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2015-03-27 09:59:29 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27284908  

Done. See commit 7455682.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-03-27 10:00:05 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27284947  

Done. See commit 90c2c5f.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-03-27 10:05:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#issuecomment-86887725  

I believe the PR is now ready for merging in develop and master (for 1.58).  
Once it is merged to develop I will close the three tickets (indicating 1.58 as the milestone release).

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-03-27 11:30:14 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27288520  

Shouldn't here `std::size_t` be used as below?  
  
`boost::mpl::size_t<>` could be used instead of integral constant. It'd be more clear.  
  
For signed numbers number of bits is decreased by 1 (`-1`). Shouldn't it be the same?  
  
I'm wondering, what's the representation of a number in MultiPrecision? I'm assuming that the space isn't increased by one bit but something bigger, e.g. a byte or more and the number is truncated somehow. I'm guessing that this can add an overhead. Wouldn't it be better to add the whole byte for unsigned or even multiply the `size` by 3? So e.g.: `CHAR_BIT * (2 * sizeof(T) + (is_unsigned ? 1 : 0))`. Or maybe even better find the next power of 2 dividable by CHAR_BIT (which can be greater than 8). In case if some optimization was done in MultiPrecision, i.e. numbers stored on an array of types as big as possible or something like that. So e.g. for 128b unsigned, use 256b promoted signed. Depending on the representation. Anyway, it could be added later.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-03-27 11:35:57 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27288733  

Missing `unsigned_long_long` and `uint128_type`.  
If the above was added would std::size_t still be needed? Well, I guess it shouldn't harm.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-03-27 12:14:28 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27290248  

- `cpp_int_backend<>` expects `unsigned int`, which is why I used it here. For this reason I cannot really use `mpl::size_t` here.  
- `mpl::size_t` can be used in other places though. Will make the change when appropriate.  
- No, you may not decrease the number of bits by 1 for unsigned numbers. Let me give you an example in binary. Suppose you have 3-bit unsigned numbers: they range from `0` to `111`. If you multiply `111 * 111` you get `110001`, which is well above what you can get by considering 5-bit unsigned numbers (here `5 = 2 * 3 - 1`).  
- I am pretty sure that multiprecision numbers increase their bit sizes in chunks. My point of view is that what you describe is the responsibility of the multiprecision number to handle not ours. But yes, we can go over this again later.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-03-27 12:15:50 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27290314  

I would like to keep `std::size_t` anyways, as it is guaranteed to exist (as defined type).  
For `unsigned_long_long` and `uint128_type` I could not find the corresponding BOOST macros. If you can point me to them I can add support for them as well.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-03-27 12:20:50 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27290600  

Two instances of `boost::integral_constant<>` have been replaced by `boost::mpl::size_t<>`.  
See commit 88009aa.

---

## Comment 18

> Username: awulkiew  
> Created_at: 2015-03-27 14:06:15 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27296803  

Those are the same as for signed types (https://github.com/boostorg/config/blob/master/include/boost/config/suffix.hpp line 481)

---

## Comment 19

> Username: awulkiew  
> Created_at: 2015-03-27 14:19:48 UTC  
> Updated_at: 2015-03-27 20:47:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27297881  

- I mainly suggested that to avoid warnings about conversion from size_t to unsigned on x64 because sizeof() returns std::size_t (can those warnings be generated also for compile-time constants?). Anyway maybe size_t should then be used here and then converted when it's passed into the cpp_int_backend?  
- ok great!  
- My point was that in the code the number of bits is decreased for signed promoted to signed (`(is_unsigned ? 1 : -1)`) which seems to be wrong. The number of bits should probably be exactly 2_bits also for signed, not 2_bits-1. Or am I missing something?  
- Yes, that's reasonable assumption. Though I have a feeling that some things can be left for the user, e.g. in the examples where Min==Max and void must be passed as the Allocator, Max is always set to the power of 2. My guess is that this should be the fastest. As said, we can postpone it.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-03-27 15:18:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#issuecomment-86971731  

@awulkiew Will work on your latest comments later this evening.

---

## Comment 21

> Username: mkaravel  
> Created_at: 2015-03-27 20:48:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27329870  

Thanks a lot for the link.  
Support for `unsigned long long` and `uint128` has now been added.  
See commits af1a3da and 27e7ad6.

---

## Comment 22

> Username: mkaravel  
> Created_at: 2015-03-27 20:56:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27330498  

- Let's address the conversion warnings if they pop-up during testing.  
- Suppose you have _b_-bit signed integers. One bit is used for the sign, which leaves _b_-1 bits for the magnitude. When you do promotion (where promotion is targeted towards avoiding overflow, as we do here) you need 2 \* (_b_-1) bits for the magnitude of the promoted type and of course one bit for the sign, hence a total of 2 \* _b_-1 bits.

---

## Comment 23

> Username: awulkiew  
> Created_at: 2015-03-27 21:54:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#discussion_r27333827  

That's rather strict, I think the number of bits should be a power of 2 or at least should be multiple of CHAR_BIT, but maybe indeed multiprecision takes this into account. Anyway, we can test and if needed work on it later.

---

## Comment 24

> Username: awulkiew  
> Created_at: 2015-03-27 21:55:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/274#issuecomment-87103113  

Thanks for the PR! Merging.

---
