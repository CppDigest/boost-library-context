# #305 [buffer] Replace int with signed_index_type. [Merged]

> Username: awulkiew  
> Created at: 2015-06-05 11:45:36 UTC  
> Updated at: 2015-06-08 19:19:16 UTC  
> Merged at: 2015-06-08 19:19:14 UTC  
> Closed at: 2015-06-08 19:19:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/305  



---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-06-05 21:08:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#issuecomment-109441212  

Thanks for the PR!

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-06-05 21:08:57 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31851827  

counts are not indexes.  
Can you make them std::size_t, besides count_in_original which should stay int?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-06-05 21:09:09 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31851843  

These will be removed

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-06-05 21:09:24 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31851865  

Will be removed

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-06-05 21:11:33 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31852013  

offsetted_count is a count but used in calculations with indexes. What do we normally do here?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-06-05 21:13:02 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31852108  

This should IMO stay int. It is not an index and not used as an index.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-06-05 21:14:13 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31852193  

section begin_index/end_index should be made signed_index_type too.  
But that can be done separately, indeed.  
So this signed_index_type here is OK

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-06-05 21:14:32 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31852219  

should stay int

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-06-05 21:23:58 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31852966  

Why it should stay `int`? It's used to count the elements of a vector meeting some criteria. This means that it should be at least of size of `std::vector<robust_original>::size_type`. But since it can also be decremented, to be perfectly safe it could be replaced by 2 variables, one counting the elements meeting the `m_is_interior` condition and the other one counting the elements meeting the `m_has_interior` condition. And at the end both values should be compared which one is greater. Do you agree?

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-06-05 21:30:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#issuecomment-109445578  

Btw, why `turn_index` is initialized to `-1` and therefore must be a signed type? Can e.g. `buffer_turn_info` be created when there are no turns?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-06-05 21:33:36 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31853636  

Sure. I didn't know exactly which variables are used to count in one direction and which count in both so I chose a safe approach.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-06-05 21:38:03 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31853977  

Again, why a value returned by a Range::size() whatever the Range is (probably std::vector) should be assigned to an `int`?

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-06-05 21:42:00 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31854258  

Well, it's initialized with `-1` (Is that correct?). So I'm guessing that it should be a signed type (unless it shouldn't be initialized to `-1`). And it's again, incremented in a loop iterating through `pc.robust_turns` elements so it should be at least of size of the `std::vector<robust_turn>::size_type`.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-06-05 21:49:04 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31854743  

Indeed, also the type of `non_duplicate_index` and of various variables in the sectionalize code.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2015-06-05 21:51:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#issuecomment-109448944  

Since this `signed_index_type` can be used for various things. And since it's defined as `boost::make_signed<std::size_t>::type`. Maybe then should it be renamed to `signed_size_type` or `signed_size_t`? Or maybe should this additional type be added. Then it could be e.g. used for signed counters and be less confusing.

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2015-06-06 09:29:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#issuecomment-109556023  

I'm in favour of renaming it to signed_size_type because index explicitly names a target which often is not appropriate and indeed confusing.  
If that is done, most of your changes would be OK.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2015-06-08 10:21:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#issuecomment-109942852  

Ok, I changed the name to signed_size_type and used std::size_t for most of the counters. Some of them are indeed not used. The changes that should be done in other parts of the library, e.g. sectionalize shouldn't be a part of this PR so I'd stop here. Could `turn_index` be made `std::size_t` as well or should it stay signed?

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2015-06-08 18:01:31 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31941700  

Thanks!  
The filename should be renamed too.  
I don't understand that I only see buffer-things, if this renaming was part of this PR, then I would expect much more changes? Where are they?

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2015-06-08 18:05:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#issuecomment-110093996  

I'm OK with merging.  
About turn_index, I agree, it is better to make it unsigned and initialize it with 0

---

## Comment 20

> Username: awulkiew  
> Created_at: 2015-06-08 18:17:11 UTC  
> Updated_at: 2015-06-08 19:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#discussion_r31943255  

I only changed the names in the buffer right now, since the refactoring of other parts wasn't the purpose of this PR. Therefore I only added a type and didn't rename a file. I thought it'd be better to change the rest in the other PR or to do it directly in the develop since it would be rather automatic. Of course the refactoring of `sectionalize` and posibly other parts of the library would require a PR.

---

## Comment 21

> Username: awulkiew  
> Created_at: 2015-06-08 19:13:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/305#issuecomment-110110756  

Done, I amended the commit for clear history.

---
