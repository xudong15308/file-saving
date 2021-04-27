//下棋源代码
#include <stdio.h>
#include <ctype.h>
#define SIZE 8
void display(char board[][SIZE]);
int valid_moves(char board[][SIZE],int moves[][SIZE],char player);
void make_move(char board[][SIZE],int row,int col,char player);
void computer_move(char board[][SIZE],int moves[][SIZE],char player);
int get_score(char board[][SIZE],char player);
int best_move(char board[][SIZE],int moves[][SIZE],char player);
void main()
{
 char board[SIZE][SIZE]={0};
 int moves[SIZE][SIZE]={0};
 int row=0;
 int col=0;
 int no_of_games=0;
 int no_of_moves=0;
 int invalid_moves=0;
 int comp_score=0;
 int user_score=0;
 char y=0;
 char x=0;
 char again=0;
 int player=0;
printf("●玩法说明\n");
printf("1.开辟新局者持黑子，加入者持白子。\n");
printf("  我先执白棋 - (*)\n  你执黑棋 - (0).\n");
printf("\n祝你好运! 请按enter键，进行游戏.\n");
scanf("%c",&again);
 do
 {
  player=++no_of_games%2;
  no_of_moves=4;
  for(row=0;row<SIZE;row++)
   for(col=0;col<SIZE;col++)
    board[row][col]=' ';
  board[SIZE/2-1][SIZE/2-1]=board[SIZE/2][SIZE/2]='0';
  board[SIZE/2-1][SIZE/2]=board[SIZE/2][SIZE/2-1]='*';
  do
  {display(board);
   if(player++%2)
   {
    if(valid_moves(board,moves,'0'))
    {
     for(;;)
     {
      fflush(stdin);
      printf("输入你要下子的位置(行列)：: ");
      scanf("%d%c",&x,&y);
      y=tolower(y)-'a';
      x--;
      if(x>=0&&y>=0&&x<SIZE&&y<SIZE&&moves[x][y])
      {
       make_move(board,x,y,'0');
       no_of_moves++;
       break;
      }
      else
       printf("不是有效位置，请重新输入。\n");
     }
    }
    else
     if(++invalid_moves<2)
     {
      fflush(stdin);
      printf("\n没有可下位置，电脑下棋。\n");
      scanf("%c",&again);
     }
     else
      printf("\n没有多余位置，本局游戏结束。\n");
   }
   else
   {
    if(valid_moves(board,moves,'*'))
    {
     invalid_moves=0;
     computer_move(board,moves,'*');
     no_of_moves++;
    }
    else
    {
     if(++invalid_moves<2)
      printf("\n没有可下位置，你下棋。\n");
     else
      printf("\n没有多余位置，本局游戏结束。\n");
    }
   }
  }while(no_of_moves<SIZE*SIZE&&invalid_moves<2);
  display(board);
  comp_score=user_score=0;
  for(row=0;row<SIZE;row++)
   for(col=0;col<SIZE;col++)
   {
    comp_score+=board[row][col]=='*';
    user_score+=board[row][col]=='0';
   }
  printf("最后分数：");
  printf("电脑： %d\t玩家： %d\n\n",comp_score,user_score);
  fflush(stdin);
  printf("你还想玩一次吗？(y/n):");
  scanf("%c",&again);
 }while(tolower(again)=='y');

 printf("\n谢谢使用此游戏。\n");
}
void display(char board[][SIZE])
{
 int row=0;
 int col=0;
 char col_label='a';
 printf("\n ");
 for(col=0;col<SIZE;col++)
  printf("   %c",col_label+col);
 printf("\n");
 for(row=0;row<SIZE;row++)
 {
  printf("  +");
  for(col=0;col<SIZE;col++)
   printf("---+");
  printf("\n%2d|",row+1);
  for(col=0;col<SIZE;col++)
   printf(" %c |",board[row][col]);
  printf("\n");
 }
 printf("  +");
 for(col=0;col<SIZE;col++)
  printf("---+");
 printf("\n");
}
int valid_moves(char board[][SIZE],int moves[][SIZE],char player)
{
 int rowdelta=0;
 int coldelta=0;
 int row=0;
 int col=0;
 int x=0;
 int y=0;
 int no_of_moves=0;
 char opponent=(player=='0')?'*':'0';
 for(row=0;row<SIZE;row++)
  for(col=0;col<SIZE;col++)
   moves[row][col]=0;
 for(row=0;row<SIZE;row++)
  for(col=0;col<SIZE;col++)
  {
   if(board[row][col]!=' ')
    continue;
   for(rowdelta=-1;rowdelta<=1;rowdelta++)
    for(coldelta=-1;coldelta<=1;coldelta++)
    {
     if(row+rowdelta<0||row+rowdelta>=SIZE||
        col+coldelta<0||col+coldelta>=SIZE||
            (rowdelta==0&&coldelta==0))
      continue;
     if(board[row+rowdelta][col+coldelta]==opponent)
     {
      x=row+rowdelta;
      y=col+coldelta;
      for(;;)
      {
       x+=rowdelta;
       y+=coldelta;
       if(x<0||x>=SIZE||y<0||y>=SIZE)
        break;
       if(board[x][y]==' ')
        break;
       if(board[x][y]==player)
       {
        moves[row][col]=1;
        no_of_moves++;
        break;
       }
      }
     }
    }
  }
  return no_of_moves;
}
void make_move(char board[][SIZE],int row,int col,char player)
{
 int rowdelta=0;
 int coldelta=0;
 int x=0;
 int y=0;
 char opponent=(player=='0')?'*':'0';
 board[row][col]=player;
 for(rowdelta=-1;rowdelta<=1;rowdelta++)
  for(coldelta=-1;coldelta<=1;coldelta++)
  {
   if(row+rowdelta<0||row+rowdelta>=SIZE||
      col+coldelta<0||col+coldelta>=SIZE||
          (rowdelta==0&&coldelta==0))
    continue;
   if(board[row+rowdelta][col+coldelta]==opponent)
   {
    x=row+rowdelta;
    y=col+coldelta;
    for(;;)
    {
     x+=rowdelta;
     y+=coldelta;
     if(x<0||x>=SIZE||y<0||y>=SIZE)
      break;
     if(board[x][y]==' ')
      break;
     if(board[x][y]==player)
     {
      while(board[x-=rowdelta][y-=coldelta]==opponent)
       board[x][y]=player;
      break;}
    }
   }
  }
}
int get_score(char board[][SIZE],char player)
{
 int score=0;
 int row=0;
 int col=0;
 char opponent=player=='0'?'*':'0';
 for(row=0;row<SIZE;row++)
  for(col=0;col<SIZE;col++)
  {
   score-=board[row][col]==opponent;
   score+=board[row][col]==player;
  }
 return score;
}
int best_move(char board[][SIZE],int moves[][SIZE],char player)
{
 int row=0;
 int col=0;
 int i=0;
 int j=0;
 char opponent=player=='0'?'*':'0';
 char new_board[SIZE][SIZE]={0};
 int score=0;
 int new_score=0;
 for(row=0;row<SIZE;row++)
  for(col=0;col<SIZE;col++)
  {
   if(!moves[row][col])
    continue;
   for(i=0;i<SIZE;i++)
    for(j=0;j<SIZE;j++)
     new_board[i][j]=board[i][j];
   make_move(new_board,row,col,player);
   new_score=get_score(new_board,player);
   if(score<new_score)
    score=new_score;
  }
  return score;
}
void computer_move(char board[][SIZE],int moves[][SIZE],char player)
{
 int row=0;
 int col=0;
 int best_row=0;
 int best_col=0;
 int i=0;
 int j=0;
 int new_score=0;
 int score=100;
 char temp_board[SIZE][SIZE];
 int temp_moves[SIZE][SIZE];
 char opponent=player=='0'?'*':'0';
 for(row=0;row<SIZE;row++)
  for(col=0;col<SIZE;col++)
  {
   if(moves[row][col]==0)
    continue;
   for(i=0;i<SIZE;i++)
    for(j=0;j<SIZE;j++)
     temp_board[i][j]=board[i][j];
   make_move(temp_board,row,col,player);
   valid_moves(temp_board,temp_moves,opponent);
   new_score=best_move(temp_board,temp_moves,opponent);
   if(new_score<score)
   {
    score=new_score;
    best_row=row;
    best_col=col;
   }}
  make_move(board,best_row,best_col,player
}
