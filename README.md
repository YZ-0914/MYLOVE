# MYLOVE
something is wrong,when I succeed,I will be back and finished it.
#define _CRT_SECURE_NO_WARNINGS 1

#include<stdio.h>
#include<math.h>
#include<conio.h>
#include<stdlib.h>
#include<time.h>
#include<graphics.h>
#include<mmsystem.h>
#pragma comment(lib, "winmm.lib")//多媒体设备接口库文件
#define PI 3.1415
#define NUM 13  //烟花弹数量
#define WIDTH 960
#define HEIGHT 640

//烟花弹
struct Jet    
{
	int x, y;//坐标
	int hx, hy;//最高点的坐标
	bool isLaunch;//烟花弹是否在发射中
	DWORD t1, t2, dt;//烟花弹发射的速度
	IMAGE img[2];
	byte n : 1;//位段  0 1 0 1
}jet[NUM];

//烟花
struct Fire
{
	int r;
	int maxr;
	int x, y;
	int cenx, ceny;
	int width, height;
	int xy[240][240];

	bool show;
	bool draw;
	DWORD t1, t2, dt;
}Fire[NUM];

void welcome()
{
	mciSendString(L"open ./res/编号89757.mp3", 0, 0, 0);
	mciSendString(L"play ./res/编号89757.mp3", 0, 0, 0);
	for (int i = 0; i < 50; i++)
	{
		//清除屏幕
		cleardevice();

		int x = 500 + 180 * sin(2 * PI / 60 * i);
		int y = 200 + 180 * cos(2 * PI / 60 * i);
		//设置字体样式
		settextstyle(i, 0, L"楷体");
		settextcolor(RGB(0, 201, 0));
		setbkmode(TRANSPARENT);
		outtextxy(x, y, L"小吉吉最好看");
		Sleep(20);
	}
	_getch();//按任意键继续
	cleardevice();
	settextstyle(25, 0, L"楷体");
	outtextxy(350, 100, L"亲爱的小吉吉");
	outtextxy(350, 150, L"你 如花似玉！");
	outtextxy(350, 200, L"你 善良大方！");
	outtextxy(350, 250, L"你 似粥温柔！");
	outtextxy(350, 300, L"你 聪明可爱！");
	outtextxy(350, 350, L"你 宇宙无敌！");
	outtextxy(350, 400, L"你 好运不断！");

}

void loadImg()
{
	IMAGE jetImg;
	loadimage(&jetImg, L"./res/launch.jpg", 200, 50);
	putimage(0, 0, &jetImg);
	SetWorkingImage(&jetImg);
	for (int i = 0; i < NUM; i++)
	{
		
		int n = rand() % 5;
		getimage(&jet[i].img[0], n*20, 0, 20, 50);
		getimage(&jet[i].img[1], n*20 + 100, 0, 20, 50); 
		jet[i].isLaunch = false;
		//putimage(i * 20, 0, &jet[i].img[0]);
	}
	SetWorkingImage();
}

//产生烟花弹
void createJet()
{
	int i = rand() % NUM;//（0~13)
	if (jet[i].isLaunch == false)
	{
		jet[i].x = rand() % (WIDTH-20);
		jet[i].y = rand() % 100 + HEIGHT - 200;
		jet[i].hx = jet[i].x;
		jet[i].hy = rand() % (HEIGHT / 3 * 2);
		jet[i].isLaunch = true;
	}
	
}

//发射烟花弹
void launch()
{
	for (int i = 0; i < NUM; i++)
	{
		if (jet[i].isLaunch)
		{
			putimage(jet[i].x, jet[i].y, &jet[i].img[jet[i].n],SRCINVERT);
			//判断是否达到最高点了
			if (jet[i].y > jet[i].hy)
			{
				jet[i].y -= 5;
				jet[i].n++;
			}
			putimage(jet[i].x, jet[i].y, &jet[i].img[jet[i].n], SRCINVERT);
			if (jet[i].y <= jet[i].hy)
			{
				jet[i].isLaunch = false;
			}
		}
	}
}


int main()
{
	//创建窗口
	initgraph(960, 640);
	//printf("小吉吉，你最好看");
	//设置随机种子
	srand((unsigned)time(NULL)+clock());
	welcome();
	loadImg();
	DWORD* pMem = GetImageBuffer();//获取窗口的内存指针
	while (1);//防止闪退
	{
		for (int i = 0; i < WIDTH; i++)
		{
			for (int k = 0; k < 4; k++)
			{
				int x = rand() % WIDTH;
				int y = rand() % HEIGHT;
				if (y < HEIGHT)
				{
					pMem[y * WIDTH + x] = BLACK;
				}
			}
		}
		createJet();
		launch();
		bloom(pMem);
		Sleep(10);//sleep() 让程序休眠n毫秒
	}
	closegraph();
	return 0;
}
