# #859 - Boost native Bazel Module support (Bazel Central Registry) [Closed]

> Username: Megakuul  
> Created at: 2024-02-07 19:11:54 UTC  
> Updated at: 2024-02-17 16:47:28 UTC  
> Closed at: 2024-02-17 16:47:28 UTC  
> Url: https://github.com/boostorg/boost/issues/859  

I don't know if that's the right place to ask, just tell me if it's not.  
  
I use the Bazel build tool to build C++ projects. When I've used boost components so far, I've only created custom Bazel rules to build boost, or I've relied on third-party rulesets like [boost_rules](https://github.com/nelhage/rules_boost).  
  
With the new *bzlmod* system introduced by Bazel, the [BCR](https://registry.bazel.build/) is used to manage many Bazel modules.   
  
Is something like this planned, or would it theoretically be feasible to implement a Bazel integration into the Boost repositories?  
  
Such an integration, would largely simplify the build steps of boost components when using Bazel.
