# #440 [Hashable] hana::integral_constant with an enum value implements Hashable [Open]

> Username: Dwarfobserver  
> Created at: 2019-02-18 21:54:25 UTC  
> Updated at: 2020-09-10 19:26:14 UTC  
> Url: https://github.com/boostorg/hana/pull/440  

Hi, this is my first contribution to Boost.Hana, so please tell me my mistakes :)  
  
This small change implements Hashable for hana::integral_constant with an enumeration value :  
It allows for example to use enumeration values as key for hana::map.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2019-02-21 06:35:04 UTC  
> Updated_at: 2019-02-21 06:35:24 UTC  
> Url: https://github.com/boostorg/hana/pull/440#issuecomment-465879713  

I think CI failed because it found trailing spaces. The github site doesn't seem to reflect that though.  
  
`$ LANG=POSIX grep '[[:blank:]]$'          "${sources[@]}"; [[ $? == 1 ]] || exit 1` is the last command ran in the log but with no error message.

---

## Comment 2

> Username: Dwarfobserver  
> Created_at: 2019-02-21 07:54:02 UTC  
> Url: https://github.com/boostorg/hana/pull/440#issuecomment-465897919  

Indeed, good catch ! I corrected them.

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2019-02-22 00:58:51 UTC  
> Url: https://github.com/boostorg/hana/pull/440#issuecomment-466230699  

lgtm, but please "fixup" the commit so there is one commit.  
  
@ldionne is currently at a standards committee meeting, so I don't know when he will look at this.

---

## Comment 4

> Username: Dwarfobserver  
> Created_at: 2019-02-23 10:52:07 UTC  
> Url: https://github.com/boostorg/hana/pull/440#issuecomment-466638212  

Sorry, I thought it was only for local commits ! Done :)

---

## Comment 5

> Username: vyzaq  
> Created_at: 2020-06-17 08:14:06 UTC  
> Url: https://github.com/boostorg/hana/pull/440#issuecomment-645226024  

Do you have any progress on that PR?

---

## Comment 6

> Username: ReiserFlorian  
> Created_at: 2020-09-10 17:12:38 UTC  
> Url: https://github.com/boostorg/hana/pull/440#issuecomment-690528363  

I would like to have this feature, too

---

## Review 7 [Commented]

> Username: ricejasonf  
> Created_at: 2020-09-10 19:26:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/440#pullrequestreview-486245058  

📁 include/boost/hana/hash.hpp

```diff
  87 |+             template <typename X>
  88 |+             static constexpr auto apply(X const&) {
  89 |+                 return hana::type_c<X>;
```

> Username: ricejasonf  
> Created_at: 2020-09-10 19:26:14 UTC  
> Url: https://github.com/boostorg/hana/pull/440#discussion_r486582974  

Just taking a second look at this, is there a reason it does not normalize to `hana::type<hana::integral_constant<T, X::value>` as the others do?


---
