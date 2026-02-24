# #41 Feature/x3 keywords [Closed]

> Username: teajay-fr  
> Created at: 2014-06-29 17:25:08 UTC  
> Updated at: 2014-07-21 20:50:09 UTC  
> Closed at: 2014-07-21 20:48:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/41  

This pull request adds the keyword parser to the extensions. This only covers the case sensitive version.   
Should case insensitive behavior be necessary I can extend this in the future.  
  
I consider this version of the keywords parser as suitable to be merged into develop.  
Should something be wrong, or missing, or to be improved please let me know.

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-06-29 17:42:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#issuecomment-47462837  

Just glanced at the PR for now, but I wonder why does they `keyword` directive need an infinite tag. What is it doing that cannot be achieved by composing it with the `repeat` directive?

---

## Comment 2

> Username: teajay-fr  
> Created_at: 2014-06-29 19:47:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#issuecomment-47471169  

There is one big difference with repeat and keywords:   
- repeat operates on a single parser and checks for the right number of repetitions directly  
- whereas keywords are checked for the whole keywords group.  
  
The idea is to enable disordered keyword occurrences to be counted and validated.  
  
If I understand your suggestion correctly, we would have something like this:  
  
keywords(repeat(0,10)[kwd("a")[p1]], kwd("b")[p2], repeat(1,inf)[kwd("c")[p3]])  
  
instead of:  
  
keywords(kwd("a",0,10)[p1], kwd("b")[p2], kwd("c",1,inf)[p3]).  
  
With the way the keywords assembly now works, it shouldn't be too much trouble to change the syntax. One drawback of reusing repeat is that keywords would become much more coupled to repeat than it is right now.  
  
I'm not convinced yet that changing this design, would have some benefits.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-06-29 20:28:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#issuecomment-47473697  

I am not making any suggestions, I'm just wondering what the reason and use cases are. If I understand correctly, these bounds in keywords are the number of times than a keyword can appear, perhaps similarly to multivalued options in command line handling. If that's the case, `repeat` would make no sense here.

---

## Comment 4

> Username: teajay-fr  
> Created_at: 2014-06-29 20:29:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#issuecomment-47473797  

No problem then. We are on the same line !

---

## Comment 5

> Username: vtnerd  
> Created_at: 2014-06-30 00:57:56 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14333093  

1. Couldn't the tst_map store a std::function instead? IMO, the code would be more readable - does this version offer a performance advantage? Can the compiler see through the pointers and create a jump table where the subject parse functions are inlined? K-ballo what do you think? I think this is a similar situation to your super secret C++11 variant, did you come up with an alternative solution to this problem?  
2. If a static array is kept, I think this might be a good area to use const. Don't want to accidentally re-assign a value after initialization, or thread-safety has just been violated.

---

## Comment 6

> Username: vtnerd  
> Created_at: 2014-06-30 00:59:54 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14333114  

`int` has been used a number of places for the index into the `std::tuple`, but `std::size_t` seems more appropriate since thats the return type of the sizeof...() operator.

---

## Comment 7

> Username: vtnerd  
> Created_at: 2014-06-30 01:01:04 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14333124  

`boost::array` -> `std::array`

---

## Comment 8

> Username: K-ballo  
> Created_at: 2014-06-30 01:18:43 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14333300  

I agree, the standard library consistently uses `std::size_t` for indices.

---

## Comment 9

> Username: vtnerd  
> Created_at: 2014-06-30 01:34:38 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14333440  

1. Bit of a nitpick, but could a space be added to the function declaration -> `> keywords`? It looks very odd as-is.  
2. This forward call isn't useful because the arguments are taken by copy. Did you mean to take an r-value reference for the parameter (or rather universal reference as Scott Meyers would point out)?  
3. This could get tricky with the variadics, but I think its possible to only accept `kwd_level_1` types to this function (two type packs would be needed). If one of the types given to this function doesn't have specific member names & types it will fail to compile _later_, and the error message will be marginally harder for someone to figure out. This does limit the extensibility of the `keywords_parser` - Joel/K-ballo what do you think?

---

## Comment 10

> Username: vtnerd  
> Created_at: 2014-06-30 01:37:47 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14333465  

I forgot to mention that suggestion (3) should prevent perfect forwarding since the type inferred will not undergo reference collapsing during deduction.

---

## Comment 11

> Username: teajay-fr  
> Created_at: 2014-06-30 19:51:02 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14371246  

1. I'm not sure this is an option. I need the index to be able to index the flags and counters to perform the range checking. That would mean defining a lambda which would postpone the indexing of flags and counters farther down the call path. It's worth a try.   
2. Ok for the const.

---

## Comment 12

> Username: teajay-fr  
> Created_at: 2014-06-30 19:53:07 UTC  
> Updated_at: 2014-07-21 20:43:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#discussion_r14371338  

1. and 2. Yes of course.   
2. Looked into this, but I'm pretty sure it's not possible without adding overwhelming complexity.  
   The `kwd_level_1` struct member operator [] is being called, to pass the subject parser to the `keyword_parser` constructor. The `keywords` functions thus receives a list of `keyword_parser` types and not `kwd_level_1` types.  
   More over, it doesn't really matter if the keyword_parser is constructed inline or through the generator:  
  
`keywords(keyword_parser("key",detail::kwd_ignore_count{}))` is also valid, even if it isn't readable.

---

## Comment 13

> Username: teajay-fr  
> Created_at: 2014-07-21 20:50:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/41#issuecomment-49663506  

I think I mistargeted this pull request. I'll issue a new one for the x3-devel branch.

---
