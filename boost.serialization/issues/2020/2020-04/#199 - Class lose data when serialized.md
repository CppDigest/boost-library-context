# #199 - Class lose data when serialized [Open]

> Username: bigscientist  
> Created at: 2020-04-21 16:34:49 UTC  
> Updated at: 2020-04-21 16:42:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/199  

I apply Boost.serialization to serialize a class containing arrays and unsigned.  
I found framatically that one class can be correctly serialized and unserialized, but for an array of my class, the serialized result is each class's unsigned, the class's array is lost.###

---

## Comment 1

> Username: bigscientist  
> Created at: 2020-04-21 16:36:52 UTC  
> Updated at: 2020-04-21 16:40:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/199#issuecomment-617273515  

/*  
* Copyright (c) 2012,哈尔滨工业大学航天工程系  
* All rights reserved.  
*  
* 文件名称: Matrix.h  
* 文件标识:   
* 摘要：interface for the CMatrix class.  
*  
* 当前版本：1.1  
* 作者：hpx  
* 完成日期：2012.10.22  
*/  
  
#ifndef MATRIX_H  
#define MATRIX_H  
#include "boost/archive/binary_iarchive.hpp"  
#include "boost/archive/binary_oarchive.hpp"  
  
//#include <boost/archive/text_oarchive.hpp>  
//#include <boost/archive/text_iarchive.hpp>  
//#include <fstream>  
class Matrix    
{  
  
  
  
public:  
	void SetElem(unsigned row,unsigned col,double val);  //给单个元素赋值  
	void Setvalue(unsigned rs,unsigned cs,double *val);  //给各个元素赋值  
	void Setvalue(unsigned rs,unsigned cs);  //给定行数，列数  
	void ShowData();  
	//构造函数和析构函数  
	Matrix();  
	Matrix(unsigned r);  //自定义构造:  r维方阵  
	Matrix(unsigned rs,unsigned cs);   //自定义构造: 行数 rs  列数 cs  
	Matrix(unsigned rs,unsigned cs,double *val);  //自定义构造:  行数 rs  列数 cs  数据 val[]  
	Matrix(const Matrix & mtr);  //拷贝构造函数 ，拷贝  
	virtual ~Matrix();   //析构函数  
  
  
	bool InvertGaussJordan(	int , double  *);   //第二种求逆的方法  
	bool  InvertTrench();  //第二种求逆的方法  
  
	//运算符重载  
	double operator () (unsigned row,unsigned col);  //提取某一矩阵元素  
	Matrix &operator = (const Matrix & mtx);    //矩阵赋值运算  
	//重载友员函数  
	//friend Matrix operator + (Matrix p,Matrix q);  //加运算符   
	//friend Matrix operator - (Matrix p,Matrix q);  //减运算符   
	//friend Matrix operator * (Matrix p,Matrix q);  //乘运算符，矩阵乘矩阵   
	//friend Matrix operator * (double p,Matrix q);  //乘运算符，实数乘矩阵   
	//friend Matrix operator / (Matrix p,double q);  //除运算符，矩阵除以实数   
	//friend Matrix operator ~ (Matrix p);  //转置矩阵   
	//friend Matrix operator ! (Matrix p);  //逆矩阵   
	//friend Matrix operator - (Matrix p);  //负矩阵   
	//friend Matrix L_ (char axix,double rad);   //基元变换矩阵    axis: 轴 1:x ,2:y ,3:z    angle  角度  单位 度  
  
  
public:  
	double GetElem(unsigned row,unsigned col);  
	unsigned cols;  //矩阵列数  
	unsigned rows;  //矩阵行数  
	unsigned size;  //矩阵数据缓冲区大小  
	double *elems;   //矩阵数据缓冲区指针  
  
private:  
    // 为了能让串行化类库能够访问私有成员，所以要声明一个友元类  
    friend class boost::serialization::access;  
     //串行化的函数，这一个函数完成对象的保存与恢复  
    template<class Archive>  
    void serialize(Archive & ar, const unsigned int version)  
    {  
		unsigned i;  
		i=0;  
  
		ar & size;   //就是这么简单，也可以使用 ar<<a 这样的语法  
		/*ar & elems;*/  
		for(;i<size;i++)  
		{  
		ar & elems[i];   //就是这么简单，也可以使用 ar<<a 这样的语法  
    }  
		ar & cols;   //就是这么简单，也可以使用 ar<<a 这样的语法  
		ar & rows;   //就是这么简单，也可以使用 ar<<a 这样的语法  
	}  
  
  
};  
  
#endif // !defined(AFX_MATRIX_H__27E93BE0_F878_415E_9C3A_D356CA1A8416__INCLUDED_)

---

## Comment 2

> Username: bigscientist  
> Created at: 2020-04-21 16:37:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/199#issuecomment-617273709  

#include <iostream>  
#include <fstream>  
#include"stdio.h"  
#include<math.h>  
#include"Matrix.h"  
//#include"net.h"  
#include "boost/archive/binary_iarchive.hpp"  
#include "boost/archive/binary_oarchive.hpp"  
//#include <boost/archive/text_iarchive.hpp>  
//#include <boost/archive/text_oarchive.hpp>  
using namespace std;  
  
  
  
int main(){  
  
//测试分界  
Matrix *mytt=new Matrix[2];  
	mytt[0]=Matrix(3,3);  
		mytt[1]=Matrix(3,3);  
	mytt[0].SetElem(3,3,1);  
Matrix mytt1=mytt[0];  
mytt1.ShowData();  
Matrix mytt2=mytt[1];  
	char *filename2 = "Tsave.txt";  
std::ofstream ofs2(filename2);  
boost::archive::binary_oarchive ar2(ofs2);  
//ar2 & mytt[0];  
//ar2 & mytt[1];  
ar2 & mytt1;  
//ar2 & mytt[0];  
//ar2 & mytt1;  
//ar2 & mytt2;  
ofs2.close();  
  
	Matrix myss[3];  
Matrix my(3,3);  
std::ifstream ifs2(filename2);  
        boost::archive::binary_iarchive iar2(ifs2);  
		//iar2 & myss[0];  
		//iar2 & myss[1];  
		iar2 & myss[2];  
				//iar2 & my;  
myss[2].ShowData();  
}

---

## Comment 3

> Username: bigscientist  
> Created at: 2020-04-21 16:42:33 UTC  
> Url: https://github.com/boostorg/serialization/issues/199#issuecomment-617276150  

#ifndef MATRIX_H  
#define MATRIX_H  
#include "boost/archive/binary_iarchive.hpp"  
#include "boost/archive/binary_oarchive.hpp"  
  
//#include <boost/archive/text_oarchive.hpp>  
//#include <boost/archive/text_iarchive.hpp>  
//#include <fstream>  
class Matrix    
{  
  
  
  
public:  
	void SetElem(unsigned row,unsigned col,double val);  //给单个元素赋值  
	void Setvalue(unsigned rs,unsigned cs,double *val);  //给各个元素赋值  
	void Setvalue(unsigned rs,unsigned cs);  //给定行数，列数  
	void ShowData();  
	//构造函数和析构函数  
	Matrix();  
	Matrix(unsigned r);  //自定义构造:  r维方阵  
	Matrix(unsigned rs,unsigned cs);   //自定义构造: 行数 rs  列数 cs  
	Matrix(unsigned rs,unsigned cs,double *val);  //自定义构造:  行数 rs  列数 cs  数据 val[]  
	Matrix(const Matrix & mtr);  //拷贝构造函数 ，拷贝  
	virtual ~Matrix();   //析构函数  
  
  
	bool InvertGaussJordan(	int , double  *);   //第二种求逆的方法  
	bool  InvertTrench();  //第二种求逆的方法  
  
	//运算符重载  
	double operator () (unsigned row,unsigned col);  //提取某一矩阵元素  
	Matrix &operator = (const Matrix & mtx);    //矩阵赋值运算  
	//重载友员函数  
	//friend Matrix operator + (Matrix p,Matrix q);  //加运算符   
	//friend Matrix operator - (Matrix p,Matrix q);  //减运算符   
	//friend Matrix operator * (Matrix p,Matrix q);  //乘运算符，矩阵乘矩阵   
	//friend Matrix operator * (double p,Matrix q);  //乘运算符，实数乘矩阵   
	//friend Matrix operator / (Matrix p,double q);  //除运算符，矩阵除以实数   
	//friend Matrix operator ~ (Matrix p);  //转置矩阵   
	//friend Matrix operator ! (Matrix p);  //逆矩阵   
	//friend Matrix operator - (Matrix p);  //负矩阵   
	//friend Matrix L_ (char axix,double rad);   //基元变换矩阵    axis: 轴 1:x ,2:y ,3:z    angle  角度  单位 度  
  
  
public:  
	double GetElem(unsigned row,unsigned col);  
	unsigned cols;  //矩阵列数  
	unsigned rows;  //矩阵行数  
	unsigned size;  //矩阵数据缓冲区大小  
	double *elems;   //矩阵数据缓冲区指针  
  
private:  
    // 为了能让串行化类库能够访问私有成员，所以要声明一个友元类  
    friend class boost::serialization::access;  
     //串行化的函数，这一个函数完成对象的保存与恢复  
    template<class Archive>  
    void serialize(Archive & ar, const unsigned int version)  
    {  
		unsigned i;  
		i=0;  
  
		ar & size;   //就是这么简单，也可以使用 ar<<a 这样的语法  
		/*ar & elems;*/  
		for(;i<size;i++)  
		{  
		ar & elems[i];   //就是这么简单，也可以使用 ar<<a 这样的语法  
    }  
		ar & cols;   //就是这么简单，也可以使用 ar<<a 这样的语法  
		ar & rows;   //就是这么简单，也可以使用 ar<<a 这样的语法  
	}  
  
  
};  
  
#endif // !defined(AFX_MATRIX_H__27E93BE0_F878_415E_9C3A_D356CA1A8416__INCLUDED_)
