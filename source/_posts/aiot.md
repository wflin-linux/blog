---
title: ATM小项目
date: 2021-10-29
author: wflin
tags:
  - aiot
  - c
top: false
cover: false
toc: true
mathjax: true
summary: "demo"
categories:
  - c
---
# 递归

* 累乘 

  1. 非递归

  ```c
  // 5！= 5*4*3*2*1 
  #include <stdio.h>
  int function(int num){
  	int i;
  	int count=1;
  	for(i=num;i>=1;i--){
  		count*=i;
  	}
  	return count;
  }
  int main(){
  	printf("%d",function(5));
  	return 0;
  } 
  ```

  2. 递归

  ```c
  #include<stdio.h>
  int fac(int);
  int  fac(int n)
  {
  	if(n>1)
  	return n*fac(n-1);
  	else
  	return 1;
  }
  int main()
  {
  	int num = 5;
  //	scanf("%d",&num);
  	printf("%d!=%d\n",num,fac(num));
  //	printf("%d的累加是：%d",num,Star(num));
  	return 0;
  }
  ```

## 二维数组

```c
// 3*4 矩阵，最大值和位置输出
#include <stdio.h>
#define csize  4
#define rsize  3


int findMax(int arry[csize][rsize]){
	int i,j;
	int max = arry[0][0];
	for (i = 0; i < csize; ++i)
	{
		for ( j = 1; j < rsize; ++j)
		{
				if (max<arry[i][j])
				{
					max=arry[i][j];
				}
		}
	} 
	return max;
}
void display(int arry[csize][rsize]){
	int max = findMax(arry);
	int i,j;
	for (i = 0; i < csize; ++i)
	{
		for ( j = 0; j < rsize; ++j)
		{
				printf("%d\t",arry[i][j]);
		}
		printf("\n");
	}

	for (i = 0; i < csize; ++i)
	{
		for ( j = 1; j < rsize; ++j)
		{
				if (max==arry[i][j])
				{
					printf(" i = %d,j = %d",i,2);
				}
		}
		printf("\n");
	} 
	return;
}
int main(){
	int arry[csize][rsize]={1,6,3,4,5,6};
	display(arry);
	return 0;
}
```

# 排序

```c
#include<stdio.h>
int main(){
	int a[6]={3,5,10,7,9,1}; 
	int i,j,temp;
	printf("排序之前:\n");
	for(i=0;i<6;i++){
		printf("%d   ",a[i]);
	}
	for(i=0;i<6;i++){
		for(j=i+1;j<6;j++){
			if(a[i]<a[j]){
				temp = a[i];
				a[i] = a[j];
				a[j] = temp;
			}
		}
	}
	printf("\n排序之后:\n");
	for(i=0;i<6;i++){
		printf("%d   ",a[i]);
	}
	return 0;
}
```

## ATM

```c
/**
 * 1. 登录
 * 	创建一个账户
 * 2. 菜单
 * 3. 存钱
 * 4. 取钱
 * 5. 查询
 * 6. 退出
 */


#include<stdio.h>
#include<string.h>
#include<windows.h>
//#define _CRT_SECURE_NO_WARNINGS
#define  account   "admin"
#define  passWd   "123456"

static int count = 0;
void menu();

// 密码
int pass_word() {
	char user[10];
	char passwd[10];
	printf("请输入账户：");
	scanf("%s", &user);
	printf("请输入密码:");
	scanf("%s", passwd);
	if ((strcmp(user, account) == 0) && (strcmp(passwd, passWd) == 0)) {
		system("color 1f");
		system("cls");
		menu();
	}
	else {
		system("color 4f");
		pass_word();
	}
}

// 存钱
void saveMoney() {
	printf("请输入你要存的金额：");
	int num;
	scanf("%d",&num);
	count += num;
	printf("存入金额完成 !");
	Sleep(1000);
	system("cls");
	menu();
}

// 取钱
int getMoney() {
	printf("请输入你要取钱的金额：");
	int num;
	int flag;
	scanf("%d",&num);
	flag = num<count?1:0;
	if(flag){
		count-=num;
		printf("取钱操作成功！\n");
	}else{
		printf("余额不足!\n");
		Sleep(1000);
		system("cls");
		printf("是否需要存钱：？如果需要，请按 1,不需要任意键退出到主菜单\n");
		scanf("%d",&flag);
		if(flag==1){
			printf("正在跳转存钱接口....\n");
			Sleep(1000);
			system("cls"); 
			saveMoney();
		}menu();
	}
	Sleep(1000);
	system("cls");
	menu();
}

// 查询
int check() {
	printf("你的余额是：%d\n",count);
	system("pause");
	system("cls");
	menu();
}

// 菜单
void menu() {
	char choice;
	printf("\t\t****欢迎使用***银行ATM系统***\n");
	printf("\t\t*********1.查询余额**********\n");
	printf("\t\t*********2.存款**************\n");
	printf("\t\t*********3.取款**************\n");
	printf("\t\t*********4.退出**************\n");
	printf("\t\t请输入要操作的选项:");
	scanf("%c", &choice);
	getchar();
	printf("choice =%d",choice);
	system("cls");
	switch (choice)
	{
		case '1':check(); break;
		case '2':saveMoney(); break;
		case '3':getMoney(); break;
		case '4':exit(0); break;
		default:printf("选择错误\n");
	}
	return;
}

int main()
{
	pass_word();
	return 0;
}
```

