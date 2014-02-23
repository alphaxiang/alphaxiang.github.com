---
layout: post
title: Use Dancing Links Solve Kanoodle
tags:  algorithm
---

original ：[http://www.ams.org/samplings/feature-column/fcarc-kanoodle](http://www.ams.org/samplings/feature-column/fcarc-kanoodle)

This article will describe how to represent Kanoodle as an "exact cover problem" and how Donald Knuth implemented an algorithm to find solutions to exact cover problems. We'll also see how to solve Sudoku puzzles using these ideas....

<p class="excerpt">
<!--excerpt-->
在这篇文章里面介绍了如何通过dancing links算法完成kanoodle拼图游戏,里面对dancing links算法进行了详细的介绍.但我觉得对于
如何将kanoodle拼图游戏转化为dancing links可处理的问题,以及代码上如何实现介绍得并不详细.本文所想要做的就是介绍如何将
kanoodle拼图转化为dancing links可处理的问题,以及代码上如何实现
<!--excerpt-->
</p>
Kanoodle游戏的目的是把12块形状各不相同的小东西放入一个5\*11的矩形框内.没有空隙,也没有覆盖,如图:

<img src="http://alphaxiang.com/image/kanoodle_1.jpeg" alt="Nature">

一种可行的放置为 :           

<img src="http://alphaxiang.com/image/kanoodle_2.jpeg" alt="Nature">   

这其实可以转化为一个Exect Cover问题,有两个限制条件:

1: 5\*11的矩形框每一个框必须被覆盖

2:  每个小块必须有放在5\*11矩形框内某处

这个问题转化成Dancing Links后有5\*11\+12列,5\*11代表5\*11的矩形格子是否有被覆盖,后面的12表示12个小块有无放置进去.

其实任何一个块都能占据5\*11方格的任何一个格子,因此至少需要11\*5个点来记录哪些点被占据了,此外,每块"积木",都只能且必须用一次,因此还要记录某种放置是用掉了哪块"积木",这又需要12个数据来记录,因此如果用矩阵的行来记录可能的放置情形,那么一行需要记录11\*5\+12 = 67个数据

例如以下每一行数据表示A的一种放置. 注意最后12列是一样的,都是倒数第12列为1,这用来指示记录的是A放置后的状态:

0100000000001000000000110000000000000000000000000000000100000000000
0000000000001000000000010000000001100000000000000000000100000000000
0000000000000000000000010000000000100000000011000000000100000000000
0010000000000100000000011000000000000000000000000000000100000000000
0000000000000100000000001000000000110000000000000000000100000000000
0000000000000000000000001000000000010000000001100000000100000000000
0001000000000010000000001100000000000000000000000000000100000000000

取第一行来分析,去掉最后12个数:
0100000000001000000000110000000000000000000000000000000
再将它排成5\*11 ：                                                                                   

   01000000000                                                    
   01000000000                                                    
   11000000000                                                    
   00000000000                                                      
   00000000000                                                    

你再看1所连成的形状是不是 A代表的"积木"形状
其实到这里问题已经转换成在这个01矩阵中找12行出来,行对应相加后整行全为1,表示每个格都被占据,每个块都被用上


用Dlx解决问题到后面都是操作一个庞大的,稀疏的0/1矩阵.只要能生成这个矩阵,所有的问题就简单了.对于Kanoodle,这个矩阵的生成思路是这样;任意一个块以任意的方式放到5\*11的矩形框内时有:

找一块空白位置,放下这一块,5\*11的矩形框内某些位置将会被覆盖.记下所有可能放入情况.例如将A放入5\*11格内有很多种方法,把所有可能方法记录下来,需要记录的是放入A后哪些格子被A占据了

Kanoodle有12个小块如图:                                                
<img src="http://alphaxiang.com/image/kanoodle_3.jpeg" alt="Nature">     

由于存在平移,旋转,对称变换,因此每个小块都对应着不同的方法.


先不考虑平移,每个积木可以旋转,和镜像变换。旋转有0,90,180,270度,镜像只有两种.旋转和镜像结合共有2\*4=8种变换。
需要注意检查变换后的图案是否产生了变化,如k,L ,无论你是旋转还是镜像变换,形状都不会改变
而I ,J左右对称,因此镜像没有改变,但旋转有改变

以上图案都能放进一个4\*4的格子,因此可以用一个4\*4矩阵来表示,为便于比较变换后的图案是否与原图一样,可以左上对齐放置,及尽量将它放在一个角落里。这样就便于比较.只需比较4\*4矩阵是否相等即可知道图案是否是一样的,因为我们保证了都放在最角落的位置,无法出现平移后重合的可能

 

比如A:
A由于没有对称性,所以存在8个变换后的A,分别是A,A旋转90度,A旋转180度,A旋转270度,以及A做镜像变换后的这4种变换.需要注意的是不存在旋转45度这样的变换,旋转45度后是没法放入5\*11格子的.因此A最终可以变换成8块

4个2\*3的块,4个3\*2的块,对于一个2\*3的块的不同放法有\(11-2+1\)\*\(5-3+1\)=30种,一个3\*2的块的不同放法有

\(11-3+1\)\*\(5-2+1\)=36

因此所有A的不同放法有4\*\(30+36\)=264种,即A在这个矩阵中占据264行。其它块的不同放法可以同样计算出,只是有点块由于对称性只存在4种,2种,1种变换后的不同块,经计算,所有块的所有不同放法共有1789种,因此最后的矩阵有1789行\(与文章里面的2222行有出入,不知道是不是少计算了哪些情况还是文章有误\).

至于合法的放置到底有多少种,目前还不得而知,但目前程序已经找到的解已经上万,但这其中有些解是同构的,所有不同构的解数恐怕得借助群论的知识,考虑对称下的等价.以下是找到的一个解 
<img src="http://alphaxiang.com/image/kanoodle_4.jpg" alt="Nature">

{% highlight c %}
/******************************************************************
Aurthor: 1907767981@qq.com
Usage  : Use DLX solve Kanoodle
*******************************************************************/

#include "stdio.h"
#include "stdlib.h"
#include "string.h"

#define BOOL  char
#define TRUE  1
#define FALSE 0

typedef struct
{
    unsigned char wide;
    unsigned char length;
    unsigned char BitMap[4][4];
}Piece;

typedef struct Node
 {
    int r,c;
    struct Node *right;
    struct Node *left;
    struct Node *up;
    struct Node *Down;
}Node;

Piece Array[12]=
{
     /*  Piece A  */
    {
        2,
        3,
        {
        0,1,0,0,
        0,1,0,0,
        1,1,0,0,
        0,0,0,0,
        }
  
    },
     /*  Piece B  */
    {
       2,
       3,
       {
        0,1,0,0,
        1,1,0,0,
        1,1,0,0,
        0,0,0,0,
       }
    },
     /*  Piece C  */
    {
       2,
       4,
       {
        0,1,0,0,
        0,1,0,0,
        0,1,0,0,
        1,1,0,0,
       }
    },
     /*  Piece D  */
    {
        2,
        4,
        {
        0,1,0,0,
        0,1,0,0,
        1,1,0,0,
        0,1,0,0,
        }
  
    },
     /*  Piece E  */
    {
       2,
       4,
       {
        0,1,0,0,
        0,1,0,0,
        1,1,0,0,
        1,0,0,0,
       }
    },
     /*  Piece F  */
    {
       2,
       2,
       {
        1,0,0,0,
        1,1,0,0,
        0,0,0,0,
        0,0,0,0,
       }
    },
     /*  Piece G  */
    {
        3,
        3,
        {
        0,0,1,0,
        0,0,1,0,
        1,1,1,0,
        0,0,0,0,
        }
  
    },
     /*  Piece H  */
    {
       3,
       3,
       {
        0,0,1,0,
        0,1,1,0,
        1,1,0,0,
        0,0,0,0,
       }
    },
     /*  Piece I  */
    {
       3,
       2,
       {
        1,0,1,0,
        1,1,1,0,
        0,0,0,0,
        0,0,0,0,
       }
    },
     /*  Piece J  */
    {
        1,
        4,
        {
        1,0,0,0,
        1,0,0,0,
        1,0,0,0,
        1,0,0,0,
        }
  
    },
     /*  Piece K  */
    {
       2,
       2,
       {
        1,1,0,0,
        1,1,0,0,
        0,0,0,0,
        0,0,0,0,
       }
    },
     /*  Piece L  */
    {
       3,
       3,
       {
        0,1,0,0,
        1,1,1,0,
        0,1,0,0,
        0,0,0,0,
       }
    },
};


int nPiece;
Piece All[96];
char Table[96];
int ColoumCounter[68];
int columCounter=0;
Node *Coloum;
Node *Row;
Node Head;
BOOL flag[1789];

BOOL InitDLX() {

 int i;
 nPiece=0;
 Coloum=(Node*)malloc( 67*sizeof(Node) );
 Row=(Node*)malloc( 1789*sizeof(Node) );

 if(NULL==Coloum || NULL==Row) return FALSE;
 
 memset(All,0x00,sizeof(All));
 memset(flag,0x00,sizeof(flag));

 Head.Down=&Head;
 Head.up=&Head;
 Head.right=&Head;
 Head.left=&Head;
 
 for(i=0;i<67;i++) {

    Coloum[i].c=i;
    Coloum[i].r=0;
    ColoumCounter[i]=0;
  
    Coloum[i].Down=&Coloum[i];
    Coloum[i].up=&Coloum[i];

    //stack links insert
    Coloum[i].left=&Head;
    Coloum[i].right=Coloum[i].left->right;
    Coloum[i].left->right=&Coloum[i];
    Coloum[i].right->left=&Coloum[i];
 }
 
  for(i=0;i<1789;i++)  {

    Row[i].r=i;
    Row[i].c=67;
    Row[i].right=&Row[i];
    Row[i].left=&Row[i];
    Row[i].up=&Head;
    Row[i].Down=Row[i].up->Down;
    Row[i].up->Down=&Row[i];
    Row[i].Down->up=&Row[i];
 }
 
 return TRUE;

}

Node * GetNode() {

 Node *p;
 p=(Node*)malloc( sizeof(Node) );
 return p;
}


BOOL insertrc(int c,int r) {
	
 Node *p=GetNode();
 
 if(p==NULL)   return FALSE;
 
 
 p->c=c;
 p->r=r;
 ColoumCounter[c]++;
 
 p->left=&Row[r];
 p->right=p->left->right;
 p->left->right=p;
 p->right->left=p;

 p->up=&Coloum[c];
 p->Down=p->up->Down;
 p->up->Down=p;
 p->Down->up=p;

 return TRUE;

}

//delete whole colum
void Remove_C(int c) {
	
 Node *p;
 Node *Tr;
 Node *Tc;
 
 p=&Coloum[c];
 p->right->left=p->left;
 p->left->right=p->right;

 for(Tr=p->Down;Tr!=p;Tr=Tr->Down) {  
 	
    for(Tc=Tr->left;Tc!=Tr;Tc=Tc->left) {
      ColoumCounter[Tc->c]--;
      Tc->up->Down=Tc->Down;
      Tc->Down->up=Tc->up;
    }
    
    Tr->left->right=Tr->right;
    Tr->right->left=Tr->left;
 }
}

//resume whole colum
void Resume_C(int c) {

   Node *p;
   Node*Tr;
   Node*Tc;
 
   p=&Coloum[c];

   for(Tr=p->Down;Tr!=p;Tr=Tr->Down) {

       Tr->left->right=Tr;
       Tr->right->left=Tr;

       for(Tc=Tr->left;Tc!=Tr;Tc=Tc->left) {

          ColoumCounter[Tc->c]++;
          Tc->Down->up=Tc;
          Tc->up->Down=Tc;
       }
    }
    
    p->left->right=p;
    p->right->left=p;
}


/*left-top algin one piece  */
void Normalize(Piece *input,Piece *Output) {

     int i,j,sx,sy;

     memset(Output,0x00,sizeof(Piece));

     Output->wide=input->wide;
     Output->length=input->length;


     for(i=0;i<4;i++)
      for(j=0;j<4;j++) {
      	
          if(input->BitMap[i][j]!=0) {
              sx=i;
              i=j=4;
          }
      }

    for(i=0;i<4;i++)
      for(j=0;j<4;j++) {
      	
          if(input->BitMap[j][i]!=0) {
              sy=i;
              i=j=4;
          }
      }   
      
    for(i=0;i<input->length;i++)
      for(j=0;j<input->wide;j++) {
                Output->BitMap[i][j]=input->BitMap[i+sx][j+sy];
      }

}


void Rotate_R90(Piece *input,Piece *Output) {

    int i,j;
    Piece Temp;
    Temp.length=input->wide;
    Temp.wide=input->length;
   
    for(j=0;j<4;j++)
      for(i=0;i<4;i++)   
            Temp.BitMap[3-i][j]=input->BitMap[j][i];

   Normalize(&Temp,Output);
}


void mirror(Piece *input,Piece *output) {

  int i,j;

  memset(output,0x00,sizeof(Piece));
  output->length=input->length;
  output->wide=input->wide;
 
  for(i=0;i<input->length;i++)
       for(j=0;j<input->wide;j++) 
           output->BitMap[i][input->wide-j-1]=input->BitMap[i][j];
}


char IsSame(Piece a,Piece b) {

  int i,j;

  if((a.length!=b.length) || (a.wide!=b.wide))
      return 0;

  for(i=0;i<4;i++)
      for(j=0;j<4;j++)
         if(a.BitMap[i][j]!=b.BitMap[i][j]) return 0;
      
    return 1;
}

int PreProcess(Piece obj,int xx) {

 Piece Buf[8];
 Piece Temp,Rotated;
 int i,k,counter=0;
 int j=0;

 mirror(&obj,&Rotated);

 Temp=obj;
 Buf[counter++]=Temp;
 j=(6-Temp.length)*(12-Temp.wide);

 for(i=0;i<4;i++)  {
 
    Rotate_R90(&Temp,&Temp);

    for(k=0;k<counter;k++)  {
    
      if(1==IsSame(Buf[k],Temp)) break;
    }

    if(k==counter) Buf[counter++]=Temp;
    

 }

 Temp=Rotated;
 
 for(i=0;i<4;i++) {
 
    Rotate_R90(&Temp,&Temp);

    for(k=0;k<counter;k++)  {
    
      if(1==IsSame(Buf[k],Temp)) break;
    }

    if(k==counter) Buf[counter++]=Temp;
    

 }
 
 for(i=0;i<counter;) {
  
  All[nPiece]=Buf[i++];
  Table[nPiece++]=xx;
  }
  
 return counter;
}

void PutOnePiece(int index,int px,int py,char rowBuf[67]) {

 int i,j;
 memset(rowBuf,'0',67);
   
 rowBuf[55+Table[index]]='1';
 

 for(i=0;i<All[index].length;i++)

     for(j=0;j<All[index].wide;j++) {
     
       if(All[index].BitMap[i][j]==1) {
           rowBuf[11*(py+i)+px+j]='1';
           insertrc(11*(py+i)+px+j,columCounter);
       }
     }
     
     insertrc(55+Table[index],columCounter);
}


BOOL Kanoodle(int step)  {

 int i,j,k,h=0;
 Node *Tp;
 Node *tt;
 FILE *fpBitmap;
 char szBuf[69];
 char BitMap[67];

 //links has been empty ,get a solution
 if(Head.right==&Head) {
 
    fpBitmap=fopen("BitMap.text","r+");

    for(i=0;i<1789;i++) {
    
       if(flag[i]!=0) {

         fseek(fpBitmap, 69*i, SEEK_SET);
         fread(BitMap,67,1,fpBitmap);

         for(j=0;j<12;j++) {

            if(BitMap[55+j]=='1') break;
         }
         
         for(k=0;k<55;k++)if(BitMap[k]=='1')szBuf[k]='A'+j;

        }
    }


    for(j=0;j<55;j++) {
     if(j%11==0)printf("\n            ");
     printf("%c ",szBuf[j]);
    
    }
    
    fclose(fpBitmap);
    printf("\n");
    return TRUE;
 }

 //search a node with min colum ,this can speed up the process 
 Tp=Head.right;

 for(tt=Tp->right;tt!=&Head;tt=tt->right) {
 	
    if(ColoumCounter[tt->c]<ColoumCounter[Tp->c]) Tp=tt;
    if(ColoumCounter[Tp->c]<=1)break;
 }
  
 if(ColoumCounter[Tp->c]<1) return FALSE;// this colum contains no entry ,couldn't be a solution 
 
  i=Tp->c;
  Remove_C(i);
 
  //try  every entry
  for(Tp=Coloum[i].Down;Tp!=&Coloum[i];Tp=Tp->Down) {
  	
      Tp->right->left=Tp;
      flag[Tp->r]=1;
      
      for(tt=Tp->left;tt!=Tp;tt=tt->left) {

         if(tt->c==67) continue;

         Remove_C(tt->c);
      }

     Tp->right->left=Tp->left;
     
   if(Kanoodle(step+1)==TRUE) 
      return TRUE;
  
   else {
     //back track
     Tp->right->left=Tp;
     flag[Tp->r]=0;
    
     for(tt=Tp->left;tt!=Tp;tt=tt->left) {
        if(tt->c==67) continue;
        Resume_C(tt->c);
     }
     
     Tp->right->left=Tp->left;
   }

   Tp->left->right=Tp->right;
  } 

  Resume_C(i);
  return FALSE;
}


int main() {
    int i,j,k,h=0;
    FILE *fpBitmap;
    char szBuf[69];

    InitDLX();
    fpBitmap=fopen("BitMap.text","wr+");
    szBuf[67]='\n';
    szBuf[68]=0x00;
  
    for(i=0;i<12;i++) {
        PreProcess(Array[i],i);
    }

    for(i=0;i<nPiece;i++) {
 
       for(j=0;j<=11-All[i].wide;j++)
         for(k=0;k<=5-All[i].length;k++) {

             PutOnePiece(i,j,k,szBuf);
             columCounter++;
             fwrite(&szBuf,69,1,fpBitmap);

      }
   }
   
   fclose(fpBitmap);

   Kanoodle(0);

  return 0;
}
{% endhighlight %}
