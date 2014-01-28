---
layout: post
title: Use Dancing Links Solve Kanoodle
---

original ：[http://www.ams.org/samplings/feature-column/fcarc-kanoodle](http://www.ams.org/samplings/feature-column/fcarc-kanoodle)

This article will describe how to represent Kanoodle as an "exact cover problem" and how Donald Knuth implemented an algorithm to find solutions to exact cover problems. We'll also see how to solve Sudoku puzzles using these ideas....

在这篇文章里面介绍了如何通过dancing links算法完成kanoodle拼图游戏,里面对dancing links算法进行了详细的介绍.但我觉得对于
如何将kanoodle拼图游戏转化为dancing links可处理的问题,以及代码上如何实现介绍得并不详细.本文所想要做的就是介绍如何将
kanoodle拼图转化为dancing links可处理的问题,以及代码上如何实现

<img src="http://alphaxiang.com/image/kanoodle_1.jpeg" alt="Nature">

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

BOOL InitDLX()
{
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

Node * GetNode()
{
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
void Resume_C(int c)
{
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

void Print_Piece( Piece obj)
{
  int i,j;
  
  printf ("\n%d   %d   \n",obj.wide,obj.length);
  
  for (i=0;i<4;i++) {
    	
        for(j=0;j<4;j++)   
            printf("%d ",obj.BitMap[i][j]);
            
        printf("\n");
  }
    
  printf("\n");
}

/*left-top algin one piece  */
void Normalize(Piece *input,Piece *Output)
{
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


void Rotate_R90(Piece *input,Piece *Output)
{
    int i,j;
    Piece Temp;
    Temp.length=input->wide;
    Temp.wide=input->length;
   
    for(j=0;j<4;j++)
      for(i=0;i<4;i++)   
            Temp.BitMap[3-i][j]=input->BitMap[j][i];

   Normalize(&Temp,Output);
}


void mirror(Piece *input,Piece *output)
{
  int i,j;

  memset(output,0x00,sizeof(Piece));
  output->length=input->length;
  output->wide=input->wide;
 
  for(i=0;i<input->length;i++)
  for(j=0;j<input->wide;j++)       
      {
          output->BitMap[i][input->wide-j-1]=input->BitMap[i][j];
      }

}


char IsSame(Piece a,Piece b)
{
  int i,j;

  if((a.length!=b.length) || (a.wide!=b.wide))
      return 0;

  for(i=0;i<4;i++)
      for(j=0;j<4;j++)
      {
          if(a.BitMap[i][j]!=b.BitMap[i][j]) return 0;
      }

    return 1;
}

int PreProcess(Piece obj,int xx)
{
 Piece Buf[8];
 Piece Temp,Rotated;
 int i,k,counter=0;
 int j=0;

 mirror(&obj,&Rotated);

 Temp=obj;
 Buf[counter++]=Temp;
 j=(6-Temp.length)*(12-Temp.wide);

 for(i=0;i<4;i++)
 {
    Rotate_R90(&Temp,&Temp);

    for(k=0;k<counter;k++)
    {
      if(1==IsSame(Buf[k],Temp)) break;
    }

    if(k==counter)
    {
       Buf[counter++]=Temp;
    }

 }

 Temp=Rotated;
 for(i=0;i<4;i++)
 {
    Rotate_R90(&Temp,&Temp);

    for(k=0;k<counter;k++)
    {
      if(1==IsSame(Buf[k],Temp)) break;
    }

    if(k==counter)
    {
       Buf[counter++]=Temp;
    }

 }
  for(i=0;i<counter;)
  {
  All[nPiece]=Buf[i++];
  Table[nPiece++]=xx;
  }
  
 return counter;
}

void PutOnePiece(int index,int px,int py,char rowBuf[67])
{
 int i,j;
 memset(rowBuf,'0',67);
   
 rowBuf[55+Table[index]]='1';
 

 for(i=0;i<All[index].length;i++)
  for(j=0;j<All[index].wide;j++)
  {
   if(All[index].BitMap[i][j]==1)
   {
    rowBuf[11*(py+i)+px+j]='1';
    insertrc(11*(py+i)+px+j,columCounter);
   }
  }
  insertrc(55+Table[index],columCounter);
}


BOOL Kanoodle(int step)
{
 int i,j,k,h=0;
 Node *Tp;
 Node *tt;
 FILE *fpBitmap;
 char szBuf[69];
 char BitMap[67];


 if(Head.right==&Head)//links has been empty ,get a solution
 {

    fpBitmap=fopen("BitMap.text","r+");

    for(i=0;i<1789;i++)
    {
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
int main()
{
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
