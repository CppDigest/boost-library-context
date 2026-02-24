# #126 - Elision Only types can't be reflected in MSVC > 16.28 [Closed]

> Username: chjanke  
> Created at: 2023-05-12 06:43:21 UTC  
> Updated at: 2025-09-14 12:03:17 UTC  
> Closed at: 2025-09-14 12:03:17 UTC  
> Url: https://github.com/boostorg/pfr/issues/126  

MSVC implements mandatory copy elision in v16.29.  
  
I have expected that copy elision also enables reflecting aggregates that are neither copyable nor movable.  
Clang and GCC both successfully reflect such types, however MSVC fails to do so.  
  
Minimal example to reproduce the issue: [godbolt](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCAAzBqkAA6oCoRODB7evnrJqY4CQSHhLFEx8baY9vkMQgRMxASZPn5cFVXptfUEhWGR0XEJCnUNTdmtQ109xaUDAJS2qF7EyOwc5rHByN5YANQmsW4RqCkEAPSJVMQAdAiJifvYJhoAgk/PQ8ReDjuhqB6JAE89gB2KwvHYQn5/VCAiCzPaxAAiOywVCYXloBH2YOekKh/wBEF%2BBJ2aAYQ3MADZ4ftkVh6ARMNi3njiTDCWzAVSqTSkSjKphGczwZDOQCqTsYdEmERiLSidCuWZKTyEXSBULYjjWYrxcrJYlpbL5WLSQIKcreeqGUytW8TMDEcLXi8Pl8CDsAOLERaJEHa0W6nYsZ06kksMyhwPh2JRiGmliSZ0Op12l5vb2%2BnYAJUFSwYma8iTh/p2xDzxAY/pTzMd9pewQ9LCYwTh9tBkPtZg2VFROwA%2Bv3kHd%2B7Q8BF%2B3gFP2mMBgOXgDKmSKIRNHMgZwolA0IEN0CAQFOZ3OF0v%2BwA3fZuQv3WLYUh7MxmG87Kc7Ih1Wi0IGGHaz%2BeYIuQpPrMybdq4eBUCykJrngG5MFu0QEBAFgAPKoUIAAq/bKAAYtm/YABLPEI/aenIzzZs8oSYdg2CIv2biocoACa/bYAEACSQicahoQPuYZgeCwiR0NEr4ifQbCCAowaGOgMokECaCAjslRTukgmgWmuKQi%2B/6noyk4MIQ/aMhSoIOlYjoCZZNnVvZVk1nWOl4i%2B6mpAIZmYEMao5hWBY%2BkWbauZC5YEPmEJHCcB4XDEIARYk9D9qkABemAXleN4PMmdYvBw8y0JwACsvB%2BBwWikKgnBuNY1g7AoizLJgj6xDwpAEJoBXzAA1iAkjFVcAAcA2SJIUgAJzFcCsQTUNsTSEVHCSGVXVVZwvAKCACSdRVBWkHAsAwIgKCoCJYlkBQEBoOd9AxOew6JBeXATaOS5DP2qiUtIWDnnBmAAGp4JgADuqGGuV7U0Ji0RbRAERrREwT1ACnDtUjzDEACqERNomAOGjvA3dJBCoQw35rVgzZGOIe2kPg5YOHg54%2BWtmCqPjXiMmtjaVGtY4RMQKMeFga0EMQeAsIT8xUAYwAKEDoPg4whMyIIIhiOwUhq/IShqGtuitAYRgoHVlj6OOW2wMwbAgNFQykCz93Dj1wCzPMMLVFtHCnHupuWNYZgaBC5zRCwU6pCzAC0m0CkzzgQK4owtKQgTBL0JT9K0uRpAIyc5CkucMFMfQxOMcfVJ0IyeM0eh2PjlfDN06fTFnthN/n4xNyXmdl/MjVLCsEiFSVq109VHA7F9kjBgoD07OeL1XAY5kehAuCECQrVcLMvC7Vo8wQEgN2iXd5CUCfF0gA9I6L69ZivQDQhcJS/ZcBon3ffTmB/SsitgxDVW0NGTEDhgjOmGMUaq0gVjHGeMCbcCJmdEmZMKZ0ypoYYAtNKoMwbszVmdN2ac25og8ggg%2BZ0wFkLLGItViVXFpLaWfA5YK2BgAlWpD%2BDq1EOIbWXDdYqHUHTXQsR9CYP9lYc2AsrZwiqokL2nBfYEH3KYAOlguDAh2KHYg4ctz4Jjm0PBCck41zGKnBg6Ae4zGzoXaoncki2PSFYtu9d441A7qYlOrjG6TBbqXOuHisheO7n43uw8FiDy1iPDgpVSDlUqhPKe31Z7zzvlcB%2BOwn4vyuO/HY698Cym3rvDqXVD7HzOqfaI59roVKvlsTBZlPgMB6t/X%2BgM2HK0hrwYBsNKDgMqjA1GpDBlwIbqrYmjBSbkyGTgzA1MsF0N4LgpmLNvaVSIcgLmiyyGMiWpVKhwsMDbIYVLRBMsWH/06UA2QGteHSH4YoQRBsQBmDESbVRkibDSPgBAG27B7YEEdv0bgQ0zDdTkQojgUc9y0g%2BdYDRsd2jGIsfYtORR/E2LyOkexOdqjOLLoYtxVdGieLrhXDoIT0VhK7l0exEwGj4vCQPZq4SlqxPibwRJqghqUijsk%2BpRg8niy8M0%2BE%2BTN7ECKXvUpvU4jAiuJIIawIhoTS4PNYExUJoLQmotTgK1SBS2KgkDl60OCbReSUvah9jqHROosAgiQubVMvndUIrBVjTxScgBeS8V4%2BQILwTABSSB4H3K0fhtytb3NkI8/Wwi9AgyFokUp%2BhR5xLWhPVCXNHUelQFQJJM8WBz29Wkv1vkIDCUqZK9YO9pVWvmAgTATAsAxFkX1I1qblq8ClmYYq6bx4bVsNtS1B8Do2qQPanNzramuvdZwT1RbUlLwyVkykOT1nBolmGnWkaJDRsELGoRlURGkETUwZNVrO3sozZwLNDquaSnzQu4tPqJrpImpk5%2Ba7ckVpneJdYsRin73BY25t/Q20gA7UtfVJqJ7mp2jKztYKDUvL7bBwdwHrU/Indmp1V0XXRDdbbDg3LeXJOAMgb1XBYg5MDZu0NegI08KjTrQ9zzXlnovTwaJ16B0cDvTmx9U8eV8pngK4AQqmk9TFZWi6rUzBAcQ6BltlBon6p7Whm9Zqh0IfraQdtkgcnFQGsVIaRrJDKg0C/TtsQx4JIw4hpayH0Pacw/MJ2nk/CSCAA%3D%3D%3D)  
  
As shown in the example, the aggregate can be returned from functions just fine through mandatory copy elision, and it is also classified as an aggregate and can be aggregate initialized as expected.  
However MSVC fails, asking me to report the issue, so here it is.

---

## Comment 1

> Username: chjanke  
> Created at: 2023-05-25 06:56:45 UTC  
> Url: https://github.com/boostorg/pfr/issues/126#issuecomment-1562377379  

This seems to be a compiler bug in msvc where the C++17 elision rules don't correctly work inside decltype.  
Related report: https://developercommunity.visualstudio.com/t/C17-Guaranteed-Copy-Elision-does-not-a/10374637  
  
A workaround for this is to declare the move constructor of your type private and not implement it.  
The existence of the signature, or rather it not being explicitly deleted, is enough to make Boost.PFR work again.

---

## Comment 2

> Username: apolukhin  
> Created at: 2025-09-13 16:02:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/126#issuecomment-3288575385  

Looks like the new unsafe_declval fixes this issue. Will add tests in https://github.com/boostorg/pfr/pull/222
