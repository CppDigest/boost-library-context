# #46 - Change the "Features" listed on the intro page based on review feedback [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 20:51:04 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/46  

From Brook Milligan:  
  
- A simple Expression concept easily modeled by user code.  Member and non-member functions on Expressions can be added with compact macros, and a reference implementation exists for prototyping or experimentation.  
  
- Evaluation of expression trees matches the semantics of builtin C++ expressions as closely as possible.  All operator expressions are evaluated using the corresponding operator definitions for the types involved.  This leads to clearer understanding of the semantics of expression tree evaluation, because the definitions are local to the types involved.  This is one point at which the design of Yap diverges significantly from that of Proto; in the latter, the semantics of expression tree evaluation is determined by context classes.  Consequently, it is much more difficult to reason about the semantics in Proto than in Yap. [ This last bit could also be in a Proto v. Yap section. ]  
  
- Expression trees may be transformed explicitly in a user-defined way.  This is accomplished with overloaded member functions in a transform class, each one corresponding to subexpressions in the expression tree.  While these member functions may transform a subexpression into anything, a common pattern is to transform terminals into either new terminals or appropriate values and to leave other subexpressions unchanged.  This evaluate(transform()) idiom is expected to be one of the most common ways of using Yap to manipulate and evaluate expression trees.  Even though this use case involves copying the expression tree, it is expected that the new terminals or values will be inexpensive to copy so that the cost of transformation is small.  
  
- Functions that operate on expression trees or create expressions.  A set of functions is provided (and used by the library) to manipulate expression trees or their components.  These should simplify the process of, for example, writing user-defined transforms.  
  
- Evaluation of expression trees is generally [ what does this mean? ] equivalent to evaluating the corresponding native code.  Thus, there should be no [ true? ] performance cost to using expression trees.  
  
- Expression trees may be encapsulated within function objects that evaluate the expression when called.  This provides a nice bridge with other function-based libraries.  
  
- ... [ Maybe there are other (or fewer) features to emphasize.  The point is to expand a bit on what the features mean to the user rather than just list them. It is also to organize them linearly from most to least important/relevant to the user. ]
