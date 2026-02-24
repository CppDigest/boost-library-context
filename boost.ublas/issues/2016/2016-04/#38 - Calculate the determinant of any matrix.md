# #38 - Calculate the determinant of any matrix [Open]

> Username: richelbilderbeek  
> Created at: 2016-04-18 09:41:15 UTC  
> Updated at: 2021-03-23 12:13:02 UTC  
> Url: https://github.com/boostorg/ublas/issues/38  

Boost.uBLAS can calculate the determinant of (at most) 2x2 matrices. I have a piece of code that can do this for any matrix size (which is http://www.richelbilderbeek.nl/CppUblasMatrixExample7.htm), that already has been tested.   
  
If accepted, I volunteer to try to add this to Boost, but I'd need some help to up the quality even more.  
  
Is this already been done? If no, would it be appreciated if I (create and) submit a Pull Request?

---

## Comment 1

> Username: penguian  
> Created at: 2016-04-18 15:40:53 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-211437460  

Hi Richel,  
How does your algorithm compare in speed and stability vs (e.g.) Householder QR decomposition?  https://en.m.wikipedia.org/wiki/QR_decomposition#Connection_to_a_determinant_or_a_product_of_eigenvalues  
All the best,  Paul  
  
On 18 April 2016 7:41:16 PM AEST, Richel Bilderbeek notifications@github.com wrote:  
  
> Boost.uBLAS can calculate the determinant of (at most) 2x2 matrices. I  
> have a piece of code that can do this for any matrix size (which is  
> http://www.richelbilderbeek.nl/CppUblasMatrixExample7.htm), that  
> already has been tested.   
>   
> If accepted, I volunteer to try to add this to Boost, but I'd need some  
> help to up the quality even more.  
>   
> Is this already been done? If no, would it be appreciated if I (create  
> and) submit a Pull Request?  
>   
> ---  
>   
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly or view it on GitHub:  
> https://github.com/boostorg/ublas/issues/38  
  
##   
  
Paul Leopardi - http://sites.google.com/site/paulleopardi/

---

## Comment 2

> Username: richelbilderbeek  
> Created at: 2016-04-18 16:12:12 UTC  
> Updated at: 2016-04-18 16:15:57 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-211450046  

Undo: AFAIK I do not use Householder QR decomposition. I cannot remember the algorithm name I used. I do remember it was something using 2x2 matrices as a building block for bigger ones.

---

## Comment 3

> Username: GuillermoCasas  
> Created at: 2016-04-19 13:56:23 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-211932115  

I have been using this algorithm in my research and has given me no problems for inverting 10x10 matrices coming from a spacial least-squares polynomial fitting problem. It seems reasonably fast, although I've only used it in small systems (about 25 times per node, for about 2500 nodes).  
Well done, Richel.

---

## Comment 4

> Username: abhishekkranjan  
> Created at: 2021-03-14 10:01:34 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-798879820  

Am not getting pr, can you please help how can  i contribute my code to calculate the determinant of any matrix.  
//My cpp code  
// C++ program to find Deteminant of a matrix  
#include <bits/stdc++.h>  
using namespace std;  
  
// Dimension of input square matrix  
#define N 4  
// Function to get determinant of matrix  
int determinantOfMatrix(int mat[N][N], int n)  
{  
	int num1, num2, det = 1, index,  
					total = 1; // Initialize result  
  
	// temporary array for storing row  
	int temp[n + 1];  
  
	// loop for traversing the diagonal elements  
	for (int i = 0; i < n; i++)   
	{  
		index = i; // initialize the index  
  
		// finding the index which has non zero value  
		while (mat[index][i] == 0 && index < n)   
		{  
			index++;  
		}  
		if (index == n) // if there is non zero element  
		{  
			// the determinat of matrix as zero  
			continue;  
		}  
		if (index != i)   
		{  
			// loop for swaping the diagonal element row and  
			// index row  
			for (int j = 0; j < n; j++)   
			{  
				swap(mat[index][j], mat[i][j]);  
			}  
			// determinant sign changes when we shift rows  
			// go through determinant properties  
			det = det * pow(-1, index - i);  
		}  
  
		// storing the values of diagonal row elements  
		for (int j = 0; j < n; j++)   
		{  
			temp[j] = mat[i][j];  
		}  
		// traversing every row below the diagonal element  
		for (int j = i + 1; j < n; j++)   
		{  
			num1 = temp[i]; // value of diagonal element  
			num2 = mat[j][i]; // value of next row element  
  
			// traversing every column of row  
			// and multiplying to every row  
			for (int k = 0; k < n; k++)   
			{  
				// multiplying to make the diagonal  
				// element and next row element equal  
				mat[j][k]  
					= (num1 * mat[j][k]) - (num2 * temp[k]);  
			}  
			total = total * num1; // Det(kA)=kDet(A);  
		}  
	}  
  
	// mulitplying the diagonal elements to get determinant  
	for (int i = 0; i < n; i++)   
	{  
		det = det * mat[i][i];  
	}  
	return (det / total); // Det(kA)/k=Det(A);  
}  
  
// Driver code  
int main()  
{  
	/*int mat[N][N] = {{6, 1, 1},  
						{4, -2, 5},  
						{2, 8, 7}}; */  
  
	int mat[N][N] = { { 1, 0, 2, -1 },  
					{ 3, 0, 0, 5 },  
					{ 2, 1, 4, -3 },  
					{ 1, 0, 5, 0 } };  
  
	// Function call  
	printf("Determinant of the matrix is : %d",  
		determinantOfMatrix(mat, N));  
	return 0;  
}

---

## Comment 5

> Username: richelbilderbeek  
> Created at: 2021-03-16 07:53:06 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-800038031  

Hi @abhishekkranjan, this is not the place to post such question :-). Try a forum instead :+1:

---

## Comment 6

> Username: bassoy  
> Created at: 2021-03-18 19:09:36 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-802215834  

> Hi @abhishekkranjan, this is not the place to post such question :-). Try a forum instead +1  
  
yes. we can discuss it either directly in gitter: https://gitter.im/boostorg/ublas  
or use the mailing list: ublas@lists.boost.org

---

## Comment 7

> Username: abhishekkranjan  
> Created at: 2021-03-23 12:10:00 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-804850670  

> Hi @abhishekkranjan, this is not the place to post such question :-). Try a forum instead 👍  
  
am really sorry for posting that question, I was unaware of it.

---

## Comment 8

> Username: abhishekkranjan  
> Created at: 2021-03-23 12:13:02 UTC  
> Url: https://github.com/boostorg/ublas/issues/38#issuecomment-804852434  

> > Hi @abhishekkranjan, this is not the place to post such question :-). Try a forum instead +1  
>   
> yes. we can discuss it either directly in gitter: https://gitter.im/boostorg/ublas  
> or use the mailing list: [ublas@lists.boost.org](mailto:ublas@lists.boost.org)  
  
thanks, i have joined the gitter room
