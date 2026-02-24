# #49 - Remove tag types as independent types; instead construct them from kinds [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 22:40:28 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-20 07:04:23 UTC  
> Url: https://github.com/boostorg/yap/issues/49  

On Fri, Feb 16, 2018 at 4:26 PM, Peter Dimov via Boost <boost@lists.boost.org> wrote:  
Zach Laine wrote:  
In this case, something like the expression_tag<> scheme Peter recommended helps with this particular ambiguity:  
  
template <typename Tag, typename... T>  
auto operator()(expression_tag<Tag>, T &&... t)  
  
What I suggested was rather  
  
template <expr_kind Kind, typename... T>  
auto operator()(expression_tag<Kind>, T &&... t)  
  
as this enforces the necessary 1:1 correspondence between kinds and tags.  
  
Right.  I missed that the first time.  Thanks.  
￼
