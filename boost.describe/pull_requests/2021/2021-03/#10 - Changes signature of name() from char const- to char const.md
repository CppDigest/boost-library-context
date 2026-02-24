# #10 Changes signature of name() from char const* to char const[] [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2021-03-23 06:29:00 UTC  
> Updated at: 2025-11-07 14:06:55 UTC  
> Closed at: 2025-11-07 14:06:55 UTC  
> Url: https://github.com/boostorg/describe/pull/10  

* This makes it a lot easier to get the size of the string at  
compile-time, and in general will improve usage with a compile-time  
string library  
  
Signed-off-by: Julien Blanc <julien.blanc@tgcm.eu>

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-03-23 14:44:53 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-804961732  

Is `sizeof(decltype(D.name)) - 1` really a lot easier than `strlen(D.name)`? There's the problem with the latter not always being `constexpr`, sure, but if you need portability (to something that doesn't support `__builtin_strlen`) you can trivially write your own `strlen` that will be.

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-03-23 15:09:54 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-804982711  

I show how a descriptor name is turned into a compile-time Hana string here: https://lists.boost.org/Archives/boost/2020/09/250046.php  
  
Not sure how your compile-time string works, but it should be similar.

---

## Comment 3

> Username: Julien-Blanc-tgcm  
> Created_at: 2021-03-23 15:41:37 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-805008449  

I was more thinking of a library like this one: https://github.com/akrzemi1/static_string . It provides direct construction from a `char[]`, but not from a `char const*`  
  
Sure, you can write some adapters, but what for? Unless I'm missing something, returning an array gives more information directly available. I see some benefits, and fails to see drawbacks with this change, since `char[]` can silently be decayed to `char*`.

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-03-23 15:49:13 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-805014442  

I'm not 100% sure this won't create problems for a built-in compiler implementation down the road. I know that `__func__` is defined as an array and this causes trouble for compilers in constexpr contexts.

---

## Comment 5

> Username: Julien-Blanc-tgcm  
> Created_at: 2021-03-23 15:59:58 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-805023426  

Ok, i can't tell either, i'm not a compiler writer. Maybe just keep that open until you can get a definitive answer on that point. Do you want me to create an issue ?

---

## Comment 6

> Username: pdimov  
> Created_at: 2021-03-23 16:07:37 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-805029574  

I'll keep it open as a reminder.  
  
I've been looking into implementing Describe on top of the current reflection proposals ([P2320](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2021/p2320r0.pdf) and [P1240](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1240r1.pdf)) and at this point it's basically impossible because their `consteval` `name_of` function returns `std::string`, and there's no way to get a compile time `char const*` from there, not even a compile-time `string_view`. Perhaps the authors may be persuaded to switch to `char const*`, but I don't think a `char const []` will ever be possible in their framework.

---

## Comment 7

> Username: Julien-Blanc-tgcm  
> Created_at: 2021-03-23 16:13:55 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-805034526  

Wouldn't std::array<char> be a better choice in this regard (at least it's better than an std::string) ? Obviously there's a miss for a vocabulary compile time string, as it is a needed feature for any compile time reflection feature.

---

## Comment 8

> Username: pdimov  
> Created_at: 2021-03-23 16:57:42 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-805066963  

`array` isn't good because it's also not possible to get a `string_view` or `char const*` that's enough-constexpr from it (because the `array` returned from a function has automatic storage and we want to get a view/pointer to static storage from it.)  
  
The original compile-time `new` proposal allowed compile-time allocations to survive compile-time and get converted into static storage automatically; this would have allowed a compile-time `std::string` to become a `char const*` to static const storage. I suppose that's why P1240 returns `std::string` - it was designed with this ability in mind. But this feature didn't make it into C++20 because of implementation difficulties.  
  
At the moment I think that `char const*` is the least worst alternative as it can cross all bridges, maybe not very conveniently, but  at least it's possible.

---

## Comment 9

> Username: beached  
> Created_at: 2022-05-04 21:39:06 UTC  
> Url: https://github.com/boostorg/describe/pull/10#issuecomment-1117961164  

So I ran into this. I use CNTTP/NTTP for my library and string literals cannot be used there.  I had to work around like  
```cpp  
template<typename, typename>  
		struct describe_member_impl;  
  
		template<typename T, std::size_t... Is>  
		struct describe_member_impl<T, std::index_sequence<Is...>> {  
			static constexpr char const name[sizeof...( Is )]{ T::name[Is]... };  
			using type = json_link<name, traits::member_type_of_t<DAW_TYPEOF( T::pointer )>>;  
		};  
  
		template<typename T>  
		using describe_member =  
		  describe_member_impl<T,  
		                       std::make_index_sequence<std::char_traits<char>::length( T::name ) + 1>>;  
```  
  
Another nice addition to T::pointer, T::type or something like that.

---
