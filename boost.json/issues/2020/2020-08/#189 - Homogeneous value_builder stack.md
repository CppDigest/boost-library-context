# #189 - Homogeneous value_builder stack [Closed]

> Username: sdkrystian  
> Created at: 2020-08-23 16:45:26 UTC  
> Updated at: 2020-08-28 01:12:05 UTC  
> Closed at: 2020-08-28 01:12:05 UTC  
> Url: https://github.com/boostorg/json/issues/189  

We already track these in `basic_parser`, so there's no point in having people track this themselves

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-23 19:05:57 UTC  
> Url: https://github.com/boostorg/json/issues/189#issuecomment-678812010  

`value_builder` still has to track it, since it can be used without the parser.

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-24 16:53:40 UTC  
> Updated at: 2020-08-24 16:54:35 UTC  
> Url: https://github.com/boostorg/json/issues/189#issuecomment-679245415  

Preliminary research: ~7% increase on `citm_catalog`, `canada` and `twitter`, but a ~7% drop on `random` and `small`. This is from passing size to handlers as well as storing values & keys separately in `value_builder`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-24 16:55:42 UTC  
> Updated at: 2020-08-24 16:55:51 UTC  
> Url: https://github.com/boostorg/json/issues/189#issuecomment-679246431  

The research is a dead-end, because we can't use two allocations in `value_builder`. So what good is passing the size?

---

## Comment 4

> Username: sdkrystian  
> Created at: 2020-08-24 17:00:17 UTC  
> Url: https://github.com/boostorg/json/issues/189#issuecomment-679248712  

Again, this doesn't require multiple allocations.

---

## Comment 5

> Username: sdkrystian  
> Created at: 2020-08-24 17:01:13 UTC  
> Url: https://github.com/boostorg/json/issues/189#issuecomment-679249201  

We already track the size in `basic_parser`, so this eliminates a bunch of reads and writes from memory within `value_builder`

---

## Comment 6

> Username: sdkrystian  
> Created at: 2020-08-24 17:37:39 UTC  
> Url: https://github.com/boostorg/json/issues/189#issuecomment-679267742  

Storing keys as values on the stack will save memory -- to do so we need to add a "key kind" and store it as a `string`.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-08-28 01:12:05 UTC  
> Url: https://github.com/boostorg/json/issues/189#issuecomment-682269018  

This is done
