---
date: 2014-03-06 16:40:00
layout: post
title: OpenGL学习笔记4：简单物体的旋转
thread: 62
categories: 教程
tags: OpenGL

---

前言：在老师的推荐下准备系统学习下Nehe关于OpenGL的教程，在打开chm的第一眼就被作者的开源奉献精神所深深打动，这种思想与我不谋而合。

以下内容为我学习了教程之后自己的操作与总结，由于硬件与软件环境的差异，我的笔记内容在代码和描述上与原文可能存在一定出路。

----

本部分笔记记录的是告诉大家如何使用OpenGL的着色方法。

首先增加两个变量来控制这两个对象的旋转。这两个变量加在程序的开始处其他变量的后面。

```
GLfloat		rtri;						// 用于三角形的角度
GLfloat		rquad;						// 用于四边形的角度
```

接着我们修改DrawGLScene()的代码。
在原先代码的基础上，我们需要做的一些事情就是对rtri、rquad两个参数进行值的设定，以及增加glRotatef()的使用。

glRotatef(Angle,Xvector,Yvector,Zvector)负责让对象绕某个轴旋转。这个命令有很多用处。 Angle 通常是个变量代表对象转过的角度。 Xvector , Yvector 和 Zvector 三个参数则共同决定旋转轴的方向。

为了让读者更好地理解这三个方向的旋转，作者做了如下解释：

>X轴－您正在使用一台台锯。锯片中心的轴从左至右摆放(就像OpenGL中的X轴)。尖利的锯齿绕着X轴狂转，看起来要么向上转，要么向下转。取决于锯片开始转时的方向。这与我们在OpenGL中绕着X轴旋转什么的情形是一样的。

>Y轴－假设您正处于一个巨大的龙卷风中心，龙卷风的中心从地面指向天空(就像OpenGL中的Y轴)。垃圾和碎片围着Y轴从左向右或是从右向左狂转不止。这与我们在OpenGL中绕着Y轴旋转什么的情形是一样的。

>Z轴－您从正前方看着一台风扇。风扇的中心正好朝着您(就像OpenGL中的Z轴)。风扇的叶片绕着Z轴顺时针或逆时针狂转。这与我们在OpenGL中绕着Z轴旋转什么的情形是一样的。

在`glBegin(GL_TRIANGLES);`之前加上`glRotatef(rtri,0.0f,1.0f,0.0f);// 绕Y轴旋转三角形
`，在`glBegin(GL_QUADS);`之前加上`glRotatef(rquad,1.0f,0.0f,0.0f);//  绕X轴旋转四边形`。

但是这里要注意的是在绘制四边形之前要将其之前的代码改为这个，原因是为了重置模型观察矩阵。如果我们没有重置，直接调用glTranslate的话，会出现意料之外的结果。因为坐标轴已经旋转了，很可能没有朝着您所希望的方向。所以我们本来想要左右移动对象的，就可能变成上下移动了，取决于您将坐标轴旋转了多少角度。而由于使用了glLoadIdentity();函数，glTranslatef()中的函数参数值也需要进行相应更改。

```
glLoadIdentity();								// 重置模型观察矩阵
glTranslatef(1.5f,0.0f,-6.0f);					// 右移1.5单位,并移入屏幕 6.0
```

最后在`return TRUE;`之前加上

```
rtri+=0.2f;	
rquad-=0.15f;
```

搞定。如果嫌麻烦可以用下面的代码直接替换之前的DrawGLScene()代码：

```
int DrawGLScene(GLvoid)								// 从这里开始进行所有的绘制
{
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);// 清除屏幕和深度缓存
	glLoadIdentity();								// 重置当前的模型观察矩阵

	glTranslatef(-1.5f,0.0f,-6.0f);					// 左移 1.5 单位，并移入屏幕 6.0

	glRotatef(rtri,0.0f,1.0f,0.0f);					// 绕Y轴旋转三角形

	glBegin(GL_TRIANGLES);							// 绘制三角形
		glColor3f(1.0f,0.0f,0.0f);					// 设置当前色为红色
		glVertex3f( 0.0f, 1.0f, 0.0f);				// 上顶点

		glColor3f(0.0f,1.0f,0.0f);					// 设置当前色为绿色
		glVertex3f(-1.0f,-1.0f, 0.0f);				// 左下

		glColor3f(0.0f,0.0f,1.0f);					// 设置当前色为蓝色
		glVertex3f( 1.0f,-1.0f, 0.0f);				// 右下
	glEnd();										// 三角形绘制结束

	glLoadIdentity();								// 重置模型观察矩阵
	glTranslatef(3.0f,0.0f,0.0f);					// 右移3单位
	glRotatef(rquad,1.0f,0.0f,0.0f);				// 绕X轴旋转四边形

	glColor3f(0.5f,0.5f,1.0f);						// 一次性将当前色设置为蓝色
	glBegin(GL_QUADS);								// 绘制正方形
		glVertex3f(-1.0f, 1.0f, 0.0f);				// 左上
		glVertex3f( 1.0f, 1.0f, 0.0f);				// 右上
		glVertex3f( 1.0f,-1.0f, 0.0f);				// 左下
		glVertex3f(-1.0f,-1.0f, 0.0f);				// 右下
	glEnd();										// 正方形绘制结束

	rtri+=0.2f;						// 增加三角形的旋转变量
	rquad-=0.15f;					// 减少四边形的旋转变量

	return TRUE;					// 继续运行
}
```

本节笔记的完整代码下载地址：[物体旋转示例代码](/code/DataBlog_OpenGLTrsformExample.cpp)

----

如果你也对OpenGL感兴趣或者你在学习中有任何想法，欢迎在下方留言、与我交流！祝愿我们共同成长、一起进步！