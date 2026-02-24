# #372 - Aboated at boost::polygon::polygon_set_data<long long>::get() [Closed]

> Username: mleon08  
> Created at: 2022-05-31 02:07:40 UTC  
> Updated at: 2022-05-31 08:36:48 UTC  
> Closed at: 2022-05-31 08:36:48 UTC  
> Url: https://github.com/boostorg/thread/issues/372  

Executing this code aborted in version 1.66.0.  
I confirmed that aborted in version 1.79.0.  
Aborted when run in debug mode.  
  
```  
#include <stdio.h>  
#include <stdlib.h>  
#include <vector>  
  
#include <boost/polygon/polygon.hpp>  
  
namespace gtl = boost::polygon;  
int main(int argc, char* argv[])  
{  
	gtl::polygon_set_data<long long>* polyset = new gtl::polygon_set_data<long long>;  
  
	{  
		gtl::polygon_data<long long>* ppoly = new gtl::polygon_data<long long>;  
		std::vector<gtl::polygon_traits<gtl::polygon_data<long long>>::point_type> vpoint;  
		vpoint.reserve(4);  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(346862000, 438294000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(346533000, 438337000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353343000, 490071000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353673000, 490027000));  
		set_points(*ppoly, vpoint.begin(), vpoint.end());  
		(*polyset).insert(*ppoly);  
		delete ppoly;  
	}  
  
  
	{  
		gtl::polygon_data<long long>* ppoly = new gtl::polygon_data<long long>;  
		std::vector<gtl::polygon_traits<gtl::polygon_data<long long>>::point_type> vpoint;  
		vpoint.reserve(4);  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353626000, 489671000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353176000, 489730000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353223000, 490087000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353673000, 490027000));  
		set_points(*ppoly, vpoint.begin(), vpoint.end());  
		(*polyset).insert(*ppoly);  
		delete ppoly;  
	}  
  
	{  
		gtl::polygon_data<long long>* ppoly = new gtl::polygon_data<long long>;  
		std::vector<gtl::polygon_traits<gtl::polygon_data<long long>>::point_type> vpoint;  
		vpoint.reserve(4);  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(354911000, 489643000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(354791000, 489659000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(354838000, 490016000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(354958000, 490000000));  
		set_points(*ppoly, vpoint.begin(), vpoint.end());  
		(*polyset).insert(*ppoly);  
		delete ppoly;  
	}  
  
	{  
		gtl::polygon_data<long long>* ppoly = new gtl::polygon_data<long long>;  
		std::vector<gtl::polygon_traits<gtl::polygon_data<long long>>::point_type> vpoint;  
		vpoint.reserve(4);  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353296000, 489714000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353176000, 489730000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353223000, 490087000));  
		vpoint.push_back(gtl::polygon_traits<gtl::polygon_data<long long>>::point_type(353343000, 490071000));  
		set_points(*ppoly, vpoint.begin(), vpoint.end());  
		(*polyset).insert(*ppoly);  
		delete ppoly;  
	}  
  
	std::vector<gtl::polygon_data<long long>> result;  
	polyset->get(result);  
  
	delete 	polyset;  
  
  
	return 0;  
}  
  
```  
  
  
  
This is the call stack and The value of the variable when aborting.  
- The call stack  
```  
 	msvcp140d.dll!00007ffb3f51f7e6()	不明  
>	boosttest.exe!std::_Debug_lt_pred<boost::polygon::scanline_base<__int64>::less_half_edge & __ptr64,std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const & __ptr64,std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const & __ptr64>(boost::polygon::scanline_base<__int64>::less_half_edge & _Pred, const std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > & _Left, const std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > & _Right, const wchar_t * _File, unsigned int _Line) 行 1064	C++  
 	boosttest.exe!std::_Tree<std::_Tmap_traits<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > >,boost::polygon::scanline_base<__int64>::less_half_edge,std::allocator<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const ,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > >,0> >::_Insert_nohint<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const ,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > & __ptr64,std::_Tree_node<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const ,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > >,void * __ptr64> * __ptr64>(bool _Leftish, std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const ,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > & _Val, std::_Tree_node<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const ,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > >,void *> * _Newnode) 行 1742	C++  
 	boosttest.exe!std::_Tree<std::_Tmap_traits<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > >,boost::polygon::scanline_base<__int64>::less_half_edge,std::allocator<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const ,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > >,0> >::emplace<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > & __ptr64>(std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > & <_Val_0>) 行 962	C++  
 	boosttest.exe!std::map<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > >,boost::polygon::scanline_base<__int64>::less_half_edge,std::allocator<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > const ,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > > >::insert<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > & __ptr64,void>(std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::vector<std::pair<int,int>,std::allocator<std::pair<int,int> > > > & _Val) 行 212	C++  
 	boosttest.exe!boost::polygon::scanline<__int64,int,std::vector<int,std::allocator<int> > >::insert_new_edges_into_scanline() 行 1202	C++  
 	boosttest.exe!boost::polygon::scanline<__int64,int,std::vector<int,std::allocator<int> > >::scan<boost::polygon::polygon_set_data<__int64>,boost::polygon::arbitrary_boolean_op<__int64>::boolean_output_functor<boost::polygon::polygon_set_data<__int64>,std::vector<int,std::allocator<int> >,0>,std::_Vector_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::pair<int,int> > > > > >(boost::polygon::polygon_set_data<__int64> & result, boost::polygon::arbitrary_boolean_op<__int64>::boolean_output_functor<boost::polygon::polygon_set_data<__int64>,std::vector<int,std::allocator<int> >,0> rf, std::_Vector_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::pair<int,int> > > > > begin, std::_Vector_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,std::pair<int,int> > > > > end) 行 1222	C++  
 	boosttest.exe!boost::polygon::arbitrary_boolean_op<__int64>::execute<boost::polygon::polygon_set_data<__int64>,std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,int> > > >,std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,int> > > > >(boost::polygon::polygon_set_data<__int64> & result, std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,int> > > > b1, std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,int> > > > e1, std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,int> > > > b2, std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<std::pair<std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >,int> > > > e2, int op) 行 2627	C++  
 	boosttest.exe!boost::polygon::polygon_set_data<__int64>::clean() 行 37	C++  
 	boosttest.exe!boost::polygon::polygon_set_data<__int64>::get_fracture<std::vector<boost::polygon::polygon_data<__int64>,std::allocator<boost::polygon::polygon_data<__int64> > >,boost::polygon::polygon_concept>(std::vector<boost::polygon::polygon_data<__int64>,std::allocator<boost::polygon::polygon_data<__int64> > > & container, bool fracture_holes, boost::polygon::polygon_concept __formal) 行 793	C++  
 	boosttest.exe!boost::polygon::polygon_set_data<__int64>::get_dispatch<std::vector<boost::polygon::polygon_data<__int64>,std::allocator<boost::polygon::polygon_data<__int64> > > >(std::vector<boost::polygon::polygon_data<__int64>,std::allocator<boost::polygon::polygon_data<__int64> > > & output, boost::polygon::polygon_concept tag) 行 785	C++  
 	boosttest.exe!boost::polygon::polygon_set_data<__int64>::get<std::vector<boost::polygon::polygon_data<__int64>,std::allocator<boost::polygon::polygon_data<__int64> > > >(std::vector<boost::polygon::polygon_data<__int64>,std::allocator<boost::polygon::polygon_data<__int64> > > & output) 行 237	C++  
 	boosttest.exe!main(int argc, char * * argv) 行 70	C++  
```  
  
- The value of the variable  
```  
-		_Left	({coords_=0x000001ece6044590 {354838000, 490016000} }, {coords_=0x000001ece60445a0 {354958000, 490000000} })	const std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > &  
+		first	{coords_=0x000001ece6044590 {354838000, 490016000} }	boost::polygon::point_data<__int64>  
+		second	{coords_=0x000001ece60445a0 {354958000, 490000000} }	boost::polygon::point_data<__int64>  
+		[未加工ビュー]	{first={coords_=0x000001ece6044590 {354838000, 490016000} } second={coords_=0x000001ece60445a0 {354958000, ...} } }	const std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >  
		_Line	1744	unsigned int  
-		_Pred	{x_=0x0000008e9d53d428 {354838000} justBefore_=0x0000008e9d53d438 {0} pack_=0x0000008e9d53d440 {evalAtXforYret=...} }	boost::polygon::scanline_base<__int64>::less_half_edge &  
+		x_	0x0000008e9d53d428 {354838000}	__int64 *  
+		justBefore_	0x0000008e9d53d438 {0}	int *  
+		pack_	0x0000008e9d53d440 {evalAtXforYret=0.0000000000000000 evalAtXforYxIn=0.0000000000000000 evalAtXforYx1=...}	boost::polygon::scanline_base<__int64>::evalAtXforYPack *  
-		_Right	({coords_=0x000001ece6035b30 {353296040, 489714261} }, {coords_=0x000001ece6035b40 {353343000, 490071000} })	const std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> > &  
+		first	{coords_=0x000001ece6035b30 {353296040, 489714261} }	boost::polygon::point_data<__int64>  
+		second	{coords_=0x000001ece6035b40 {353343000, 490071000} }	boost::polygon::point_data<__int64>  
+		[未加工ビュー]	{first={coords_=0x000001ece6035b30 {353296040, 489714261} } second={coords_=0x000001ece6035b40 {353343000, ...} } }	const std::pair<boost::polygon::point_data<__int64>,boost::polygon::point_data<__int64> >  
```  
  
Please investigate the cause of the abort.

---

## Comment 1

> Username: mleon08  
> Created at: 2022-05-31 08:35:38 UTC  
> Url: https://github.com/boostorg/thread/issues/372#issuecomment-1141840542  

I should have posted this to boostorf/boost.  
I will post again.
