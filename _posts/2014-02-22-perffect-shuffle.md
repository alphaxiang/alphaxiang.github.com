---
layout: post
title: perfect shuffle 的一个线性复杂度实现
tags:  perfect_shuffle
---

今天又发现一个关于完美洗牌的算法。这个比较简单一些，由 microsoft的Peiyush Jain提出。

原论文：      A Simple In-Place Algorithm for In-Shuffle. 
                 Peiyush Jain, Microsoft Corporation. 
                            July 2004 
问题描述： 

所谓完美洗牌算法即是把输入为: 
a_1,a_2,...,a_n,b_1,b_2,...b_n的序列变为 

b_1,a_1,b_2,a_2.......b_n,a_n 这是in perfect shufle。相对应的还有out perfect shuffle。两者区别在于首尾元素位置变或不变。

perfect shuffle算法要求在O(n），时间内，O(1）空间内完成。 


perfect shuffle实质是一个置换。置换为：

                                       i -> 2 * i mod (2 * n+1) 

由于置换可以分解为一系列不相交的轮换之积。故如果能找出所有轮换的一个代表元则可很容易解决问题。 

如   

n=3时 输入 1  2  3  A  B  C   => A   1   B   2   C   3所对应的轮换为（1，2，4）（3，6，5） 

选代表元为1和3以及一个临时变量T： 

                       2->T,1->2 

1  2  3   A   B   C  ----------->   

                       4->1,T->4 

_  1  3   A   B   C  ----------->   

                       6->T,3->6 

A  1  3   2   B   C  -----------> 

                       5->3,T->5 
                       
A  1  _   2   B   3  -----------> 

A  1  B   2   C   3   置换完成 

因此问题就转换为求置换的轮换分解中的代表元问题了。 

文中巧妙的利用特定条件下每个不相交的轮换可有3的不同幂次生成。

 

我们分析长度2 * n=3^k-1的置换的轮换分解。 

考虑某一包含3^s（ 1 =< s < k ）的轮换。不妨记3^s为a_1，3^k记为m。

则轮换里的数分别为： 

  a_2 =  2 *  a_1 mod m 

  a_3 =  2 *  a_2 mod m; 

  a_4 =  2 *  a_3 mod m; 
    . 
    . 
    . 

  a_n = 2 *  a_n-1 mod m 

  a_1 = 2 *  a_n   mod m 


则    a_1  ≡2^n  *  a_1 mod m;   ( 最后一项中的a_n用倒数第二行乘2替代，以此类推........) 

因此每个3^s开始的一个轮换满足 :  3^s ≡3^s  *  2^n mod 3^k ,且长度为n

现假设两个不同的3^s开始的轮换存在相交的元素,记为:p

p≡3^i * 2^n mod 3^s

p≡3^j * 2^m mod 3^s  (i,j<s)

若n,m都为0,则显然i=j; 假设 i>j

否则应有:3^s ｜(3^i * 2^n -3^j * 2^m) ===>3^s ｜{ 3^i * ( 2^n-3^(j-i) * 2^m ) }

因为: gcd(3^s , ( 2^n -3^(j-i) * 2^m) )=1   注:2^n - 3^(j-i) * 2^m 只含2的幂次因子.因为由初等的数论知识可知道

a * m + b * n即m,n的线性组合只能表示gcd(m,n)的倍数.


因此上面等式不能成立.

因此每个以不同的3^i开始的轮换不会相交.

上面证明了每个3^i开始的轮换不相交,还需要计算每个3^i起始的轮换覆盖了所有的元素,这可以采用计数的方法证明.

因为每个3^i开始的轮换的长度满足:

           3^i≡3^i  * 2^N mod3^s 即2^N≡1mod3^(s-i) 

           所以N=φ(3^(s-i))=φ(3^s)/3^i   {  gcd (2,3)=1, N是满足等式最小的数}

对i从0到3-1求和就得所有轮换的元素个数为φ(3^s) . 3^s为素数,因此φ(3^s)=3^s-1,即覆盖了所有元素.

因此很容易得出各轮换的代表元就为3^0,3^1,3^2......3^i(i<k,i+1>k). 

对于2*n不等于3^k-1的情况，可以巧妙的利用这个结论完成ferfect shuffle。 

对于2 * n不等于3^k-1时，先找一个最接近2 * n且比2 * n小的2 * m=3^k-1。进行如下变换。 

把序列中m+1到n+m的子序列循环右移m位。 

A_1,A_2,A_3.......A_m-1,A_m，A_m+1......A_n，A_n+1，A_n＋2，.......A_n+m,A_n+m+1.....A_2*n   -> 

A_1,A_2,A_3.......A_m-1,A_n+1,A_n+2.....A_n+m,A_m,A_m+1......A_n+m+1................A_2*n 

然后对前2*m子序列进行上面的perfect shuffle。然后对剩下的部分进行同样处理。 

例如对于长为14的序列进行perfect shuffle置换： 

输入序列为： 

1  2  3  4  5  6  7  A  B  C  D  E  F  G           

14=2*7≠3^k.与14最接近的3^k-1是8=3^2 - 1.因此先对4+1到7+4的子序列循环右移4位得： 

1  2  3  4  A  B  C  D  5  6  7  E  F  G 

对前8位进行perfect shuffle移位后得： 

A  1  B  2  C  3  D  4  5  6  7  E  F  G 

剩下的子序列为 

5  6  7  E  F  G   

长度为6 最接近的2*m1=3^k1-1是 m=1 

因此对 1+1 到3+1进行循环右移1位得 

5  E  6  7  F  G 

进行2*m的perfect shuffle后得整个序列为： 

A  1  B  2  C  3   D  4   E   5    6   7   F   G 

剩下的未处理的子序列为： 

6   7   F   G 

同样的循环移位后为： 

6   F   7   G 

进行m=1的perfect shuffle得整个序列为： 

A  1  B  2  C  3  D  4  E  5  F  6   7  G 

剩下未处理的子序列为 

7   G 

长为2的轮换即交换，最后得整个序列为： 

A  1  B  2  C  3  D  4  E  5  F  6  G  7 

完成perfect shuffle。 

移位是线性时间，3^k - 1的perfect shuffle置换也是线性时间，最后的递归是对剩下的子序列进行同样的操作
因此整个过程在线性时间内完成。而且需要的辅助空间为常数－个额外临时变量。


实现代码： 

{% highlight c %}
/******************************************************************
Aurthor: 1907767981@qq.com
Usage  : an linear time complex implementation of perfect shuffle
*******************************************************************/

#include "stdio.h"
#define N 100

void Cycle(int Data[],int Lenth,int Start) {

    int Cur_index,Temp1,Temp2;

    Cur_index=(Start*2)%(Lenth+1);
    Temp1=Data[Cur_index-1];
    Data[Cur_index-1]=Data[Start-1];
  
    while(Cur_index!=Start)  {
         Temp2=Data[(Cur_index*2)%(Lenth+1)-1];
         Data[(Cur_index*2)%(Lenth+1)-1]=Temp1;
         Temp1=Temp2;
         Cur_index=(Cur_index*2)%(Lenth+1);
     }
}

 
void Reverse(int Data[],int Len) {

  int i,Temp;

  for(i=0;i<Len/2;i++)  {
  
     Temp=Data[i];
     Data[i]=Data[Len-i-1];
     Data[Len-i-1]=Temp;
  }
}

void ShiftN(int Data[],int Len,int N) {

   Reverse(Data,Len-N);
   Reverse(&Data[Len-N],N);
   Reverse(Data,Len);
}



void Perfect1(int Data[],int Lenth) {

     int i=1;

    if(Lenth==2) {

       i=Data[Lenth-1];
       Data[Lenth-1]=Data[Lenth-2];
       Data[Lenth-2]=i;
   
       return;
     }
     
     while(i<Lenth)  {
     
        Cycle(Data,Lenth,i);
        i=i*3;
     }
}

int LookUp(int N) {

   int i=3;

   while(i<=N+1) i*=3;

   if(i>3) i=i/3;

   return i;
}

void perfect(int Data[],int Lenth) {

   int i,startPos=0;

   while(startPos<Lenth) {
   
     i=LookUp(Lenth-startPos);
     ShiftN(&Data[startPos+(i-1)/2],(Lenth-startPos)/2,(i-1)/2);
     Perfect1(&Data[startPos],i-1);
     startPos+=(i-1);
   }
}

void main() {
 
 int data[N]={0};
 int i=0;
 int n;
 
 printf("please input the number of data you wanna to test(should less than 100):\n");
 scanf("%d",&n);
 
 if(n&1) {
    printf("sorry,the number should be even ");
    return;
 }
 
 for(i=0;i<n;i++) data[i]=i+1;

 perfect(data,n);
 
 for(i=0;i<n;i++)
   printf("%d   ",data[i]);

}

{% endhighlight %}
