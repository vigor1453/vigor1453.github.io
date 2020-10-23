---
layout: post
title: "第一次的C语言"
date: 2018-07-12
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "C语言入门"
---

数模没能成功，果然差的不是一星半点，不过好在有所心理准备，不比插班生那一遭。新室友张翼飞给我介绍了一个对竞赛有所了解的兄弟，谌佳宁，他介绍我去找原来的老导师魏文栋，之前因为插班生没去他的项目。并且了解到计算中心有打ACM的队伍，打算叫上赵逸一起试试看。

腾讯云送了十五天的服务器，完事后我又去买了dreamlake.xyz，十一块一年。腾讯云服务器比较贵，一个月六十六。估计到期之后我是没钱再给续上，还是选择老薛。

这两天事儿比较多，c++和离散要学，c语言大作业还刚刚开头，链表还没用上，周五下午好好学学。新室友很不错，宿舍环境可以给9.5分，扣0.5是因为没热洗澡水。这周买了床帘和电竞椅，救救我的脖子。另外学校游泳池真的是shit。

下面是上学期学生信息管理系统的代码：

```c++
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#define n 200
struct student/*定义学生结构体数组*/
{
    int number;
    char name[20];
    float grade[3];/*三门学科的成绩*/
}stu[n];

void zhuyi()/*定义注意事项函数*/
{
    printf("请注意：\n1、必须录入一次成绩后才可以执行其他操作\n2、学生姓名中不可以有空格\n请按任意键继续\n");
    getchar();
}

void xiugai(struct student a[],int b)/*定义修改学生信息函数*/
{
    int input,i,j,kemu,xunhuan=1;
    float result;
    while(xunhuan)
    {
        printf("请输入需要修改信息的学生的学号：");
        scanf("%d",&input);
        for(i=0;i<b;i++)
        {
            if(a[i].number==input)
            {
                j=i;
                break;
            }
        }
        if(j!=i)
        {
            printf("未找到该学生。");
            //exit(0);
        }
        printf("该学生姓名为：%s\n",a[j].name);
        printf("该学生语文成绩为：%.0f\n",a[j].grade[0]);
        printf("该学生数学成绩为：%.0f\n",a[j].grade[1]);
        printf("该学生英语成绩为：%.0f\n",a[j].grade[2]);
        printf("请输入需要修改的成绩（0代表语文，1代表数学，2代表英语）：");
        scanf("%d",&kemu);
        getchar();
        printf("请输入修改后的成绩：");
        scanf("%f",&result);
        a[j].grade[kemu]=result;
        getchar();
        printf("是否要继续修改？（0代表终止，其他键代表继续）");
        scanf("%d",&xunhuan);
    }
    FILE * fp;
    fp=fopen("student.txt","w");
    if(fp==NULL)
    {
        printf("无法找到student.txt，请检查。");
        exit(0);
    }
    for(i=0;i<b;i++)
    {
        fprintf(fp,"%d",a[i].number);
        fprintf(fp,"\n");
        fprintf(fp,"%s",a[i].name);
        fprintf(fp,"\n");
        for(j=0;j<3;j++)
        {
            fprintf(fp,"%.0f",a[i].grade[j]);
            if(j!=2||i!=(b-1))
                fprintf(fp,"\n");
        }
    }
    fclose(fp);
    printf("数据已更新。\n");/*直接更新文件中的学生信息*/
}

void trans(struct student *a,struct student *b)/*定义交换函数，用于在排名函数中使用*/
{
    char mingzi[20];
    int jiaohuan,i;
    float zhongjian;
    jiaohuan=(*a).number;
    (*a).number=(*b).number;
    (*b).number=jiaohuan;
    strcpy(mingzi,(*a).name);
    strcpy((*a).name,(*b).name);
    strcpy((*b).name,mingzi);
    for(i=0;i<3;i++)
    {
        zhongjian=(*a).grade[i];
        (*a).grade[i]=(*b).grade[i];
        (*b).grade[i]=zhongjian;
    }
}

void paiming(struct student a[],int b)/*定义排名函数，支持输出某一科目的前n名，并且可以自动判断是否超出当前人数*/
{
    int judge,i,j,pin,xunhuan=1,mingci;
    float jiaohuan;
    char xingming[20];
    int xuehao;
    while(xunhuan)
    {
        printf("请输入你想看到哪一科目的前几名（0代表语文，1代表数学，2代表英语）");
        scanf("%d",&judge);
        xunhuan=0;
        if(judge!=0&&judge!=1&&judge!=2)
        {
            printf("输入不符合要求，请重新输入。\n");
            xunhuan=1;
        }
    }
    xunhuan=1;
    while(xunhuan)
    {
        printf("请输入你想看到前几名：");
        scanf("%d",&mingci);
        xunhuan=0;
        if(mingci>b)
        {
            printf("人数超出限制。系统中目前仅有%d名同学。\n请重新输入\n",b);
            xunhuan=1;
        }
    }
    for(i=0;i<b;i++)/*使用了选择排序*/
    {
        pin=i;
        for(j=i+1;j<b;j++)
        {
            if(a[j].grade[judge]>a[pin].grade[judge])
            pin=j;
        }
        if(pin!=i)
        {
            struct student *p,*q;
            p=&a[pin];q=&a[i];
            trans(p,q);/*简化了程序运行，并且在执行排名函数后不需要再次录入学生成绩*/
        }
    }
    if(judge==0)
        printf("您查看的是语文成绩排名\n");
    else if(judge==1)
        printf("您查看的是数学成绩排名\n");
    else
        printf("您查看的是英语成绩排名\n");
    for(i=0;i<mingci;i++)
    {
        printf("第%d名\n",i+1);
        printf("学号：%d\t",a[i].number);
        printf("姓名：%s\n",a[i].name);
        printf("该科目成绩为：%.2f\n",a[i].grade[judge]);
    }
}

void pingjun(struct student a[],int b)/*定义查看平均成绩函数*/
{
    int i,input,j=-1;
    float ave;
    printf("请输入要查看平均成绩的学生的学号：");
    scanf("%d",&input);
    for(i=0;i<b;i++)
    {
        if(a[i].number==input)
        {
            j=i;
            break;
        }
    }
    if(j!=i)
    {
        printf("未找到该学生。");
        exit(0);
    }
    ave=(a[j].grade[0]+a[j].grade[1]+a[j].grade[2])/3.0;
    printf("该学生姓名为：%s\n",a[j].name);
    printf("平均成绩：%.2f\n",ave);
}

void tianjia(struct student a[],int b)/*定义添加学生信息函数*/
{
    int i;
    printf("请输入待添加学生的学号：");
    scanf("%d",&a[b].number);
    printf("请输入待添加学生的姓名：");
    scanf("%s",&a[b].name);
    printf("请依次输入该学生语文、数学，英语的成绩：");
    for(i=0;i<3;i++)
        scanf("%f",&a[b].grade[i]);
    FILE * fp;
    fp=fopen("student.txt","a");
    if(fp==NULL)
    {
        printf("无法找到student.txt");
        exit(0);
    }
    fseek(fp,0,SEEK_END);
    fprintf(fp,"\n");
    fprintf(fp,"%d",a[b].number);
    fprintf(fp,"\n");
    fprintf(fp,"%s",a[b].name);
    fprintf(fp,"\n");
    for(i=0;i<3;i++)
    {
        fprintf(fp,"%.0f",a[b].grade[i]);
        if(i!=2)
            fprintf(fp,"\n");
    }
    fclose(fp);
    printf("添加成功");
}

void menu()/*定义主菜单函数*/
{
    printf("********************************\n");
    printf("**欢迎使用学生成绩管理系统v2.0**\n");
    printf("********************************\n");
    printf("********1---录入学生成绩********\n");
    printf("********2---查询学生成绩********\n");
    printf("********3---添加学生成绩********\n");
    printf("********4---修改学生成绩********\n");
    printf("********5---输出平均成绩********\n");
    printf("*********6---输出前n名**********\n");
    printf("********************************\n");
    printf("请选择你要使用的功能：");
}

int luru(struct student b[])/*定义录入学生成绩函数*/
{
    int i,j,count=0;
    FILE * fp;
    fp=fopen("student.txt","r");
    if(fp==NULL)
    {
        printf("无法找到student.txt，请检查。");
        exit(0);
    }
    for(i=0;i<n;i++)
    {
        fscanf(fp,"%d",&b[i].number);
        fscanf(fp,"%s",b[i].name);
        for(j=0;j<3;j++)
            fscanf(fp,"%f",&b[i].grade[j]);
        count++;
        if(feof(fp))
            break;
    }
    printf("本次共读取了%d名学生的成绩。\n",count);
    for(j=0;j<count;j++)
    {
        printf("学号：%d\n",b[j].number);
        printf("姓名：%s\n",b[j].name);
        printf("语文成绩：%.0f\n",b[j].grade[0]);
        printf("数学成绩：%.0f\n",b[j].grade[1]);
        printf("英语成绩：%.0f\n",b[j].grade[2]);
        printf("\n");
    }
    return count;
}

void shuchu(struct student b[],int a)/*定义输出学生成绩函数*/
{
    FILE * fp;
    int i,j;
    fp=fopen("student.txt","r");
    if(fp==NULL)
    {
        printf("无法找到student.txt");
        exit(0);
    }
    for(i=0;i<n;i++)
    {
        if(b[i].number==a)
        {
            printf("该学生的姓名为：");
            puts(b[i].name);
            printf("该学生的语文成绩为：");
            printf("%.2f\n",b[i].grade[0]);
            printf("该学生的数学成绩为：");
            printf("%.2f\n",b[i].grade[1]);
            printf("该学生的英语成绩为：");
            printf("%.2f\n\n",b[i].grade[2]);
            break;
        }
    if(i==n-1)
        printf("未找到该学生。\n");
    }
}

void main()/*主函数*/
{
    char s[100];
    while(1)
    {
        printf("请输入密码：");
        gets(s);
        if(strcmp(s,"denghaoran"))
            puts("无效密码");
        else
            break;
    }
    puts("通过\n\n");/*设计了一个小密码*/
    char panduan[10];
    int i,xunhuan=1,judge,renshu;
    int number;
    zhuyi();
    while(xunhuan)
    {
        menu();
        scanf("%d",&judge);
        if(judge==1)
            renshu=luru(stu);/*调用录入函数并获得人数的返回值*/
        else if(judge==2)
        {
            printf("请输入学生学号：");
            scanf("%d",&number);
            shuchu(stu,number);/*调用输出函数*/
        }
        else if(judge==3)
        {
            tianjia(stu,renshu);/*调用添加学生信息函数*/
        }
        else if(judge==4)
        {
            xiugai(stu,renshu);
        }
        else if(judge==5)
        {
            pingjun(stu,renshu);/*调用平均成绩函数*/
        }
        else if(judge==6)
        {
            paiming(stu,renshu);/*调用排名函数*/
        }
        else
            printf("输入不符合要求\n");
        printf("是否需要继续使用？\n");
        puts("yes no\n");
        getchar();/*防止系统误读取回车键*/
        while(1)
        {
            gets(panduan);
            if(!strcmp(panduan,"yes"))
            {
                xunhuan=1;
                break;
            }
            else
            {
                xunhuan=0;
                break;
            }
        }
    }
}
/*邓浩然制作，在2017的最后一天*/
```

注意student.txt要写成以下形式，放在c文件同一目录下即可：

denghaoran

65

23

10

记得删除student.txt最后一行的空格，很要命。