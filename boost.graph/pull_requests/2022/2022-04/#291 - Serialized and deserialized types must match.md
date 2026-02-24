# #291 Serialized and deserialized types must match [Open]

> Username: olologin  
> Created at: 2022-04-11 18:52:42 UTC  
> Updated at: 2025-12-05 20:17:04 UTC  
> Url: https://github.com/boostorg/graph/pull/291  

In our project at work we use custom boost archive for serialization and deserialization, and unsigned integers are serialized in a non-trivial way, which does not match with serialization of signed integer. This causes some troubles.  
  
I guess it is fine to change this serialization method in upstream project.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-04-12 00:19:19 UTC  
> Url: https://github.com/boostorg/graph/pull/291#issuecomment-1095725653  

Hi @olologin , thanks for your interest in improving Boost.Graph. I don't really understand the problem here with the return type of `num_vertices` and `num_edges`. Are you saying that the value overflows signed integer, and that's why it needs to be unsigned?  
Can you possibly open an issue with instructions for how to reproduce the problem?

---

## Comment 2

> Username: olologin  
> Created_at: 2022-04-12 18:24:37 UTC  
> Updated_at: 2022-04-12 18:27:03 UTC  
> Url: https://github.com/boostorg/graph/pull/291#issuecomment-1097049092  

@jeremy-murphy   
boost::serialization has requirement to always serialize and deserialize the same types in the same order.  
In case with this particular serialization in adj_list_serialize this simple requirement is violated.  
  
It is not easy to reproduce failure here, because in our company we override serialization of unsigned and signed types in boost archive, and we override it differently for signed and unsigned types, and this is why nobody else sees a problem in adj_list_serialize.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2022-04-12 23:56:04 UTC  
> Url: https://github.com/boostorg/graph/pull/291#issuecomment-1097393365  

I want to help, but I'm not sure whether this is actually a problem in Boost.Graph, or a problem in Boost.Serialization, or a problem in your code. I can't change anything in Boost.Graph without convincing evidence that the problem is here, which usually means a minimal working example that demonstrates the issue combined with a patch that fixes it.  
You've provided a patch here, but there is no evidence that there is a problem or that this patch fixes it.

---

## Comment 4

> Username: olologin  
> Created_at: 2022-04-13 13:10:30 UTC  
> Url: https://github.com/boostorg/graph/pull/291#issuecomment-1098031863  

@jeremy-murphy   
I understand you. But you can see two functions, one is "save" and the other one is "load", first one saves "int", second one loads "unsigned int". These types are different and this is why it is broken.  
  
I will try to come up with a test to reproduce failure.

---

## Comment 5

> Username: andreacassioli  
> Created_at: 2025-12-05 20:17:04 UTC  
> Url: https://github.com/boostorg/graph/pull/291#issuecomment-3618453104  

Just a comment: looking at the code I think the issue should be solved deducing the right type from the graph traits `vertices_size_type` (which is meant to be unsigned according to the docs.). Not sure if it might break some backward compatibility.

---
