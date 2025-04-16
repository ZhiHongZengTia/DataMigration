# Qt

下载：https://download.qt.io/new_archive/qt/5.6/5.6.1/

安装：

```
chmod 777 ./*.run
sudo ./*.run
```

---

快捷键：

ctrl + n 对齐选中的代码

ctrl + i 当前行对齐



ctrl + enter  跳转到下一行

ctrl + shift + enter 跳转到上一行



---

共享文件夹设置

sudo mount -t fuse.vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other

如果每次重启之后想让系统自动挂载 #vi /etc/fstab: 在最后添加一行： .host:/ /mnt/hgfs fuse.vmhgfs-fuse allow_other 0 0



## chapter 1. 环境安装

Qt：开发库

Qt creater：IDE，集成开发环境，包含编辑器，编译器，项目的管理和浏览工具，生成工具，图形界面的调试器

Qt designer：设计图形界面的设计器，被包含在 creator 中

1. Windows

   Desktop Qt 5.6.1 MinGW 32bit

   使用了 MinGW 作为编译器

2. **Ubuntu**

需要安装一些必要的库文件，特别是 OpenGL 库

```Linux
sudo apt install build-essential libgl1-mesa-dev
sudo apt install libglu1-mesa-dev freeglut3-dev mesa-common-dev
sudo apt install libcanberra-gtk-module
```



## chapter 2. Demo



Qt Widget Application





---

程序发布 release 

debug 模式下，生成的 .exe 文件很大，并且运行需要 mingw49_32/bin 下 dll 库（动态链接库）的支持；

而在 release 模式下，文件则很小

release 版本的运行，需要打包库，用到工具 mingw49_32/bin/windemplyqt.exe

方法：

- Step 1 -> 将 release 版本的 .exe 文件复制到新建的文件夹 myfirstQt

- Step 2 -> 在 Qt 5.6 for Desktop 终端中

```C++
windeployqt e:\Zhz2023\Qt\chapter2\demo1\myfirstQt
```

执行完之后（这是动态编译，需要用到的 dll 才添加，这样文件会小一些；而静态编译会把所有的 dll 库文件合并在一起，文件较大），

文件夹 myfirstQt 中添加了运行所需要的库。该文件夹内容可在无 Qt 环境的计算机中运行。

***

修改 .exe 的图标

```
// .pro 文件
RC_ICONS = my_Ico_Name.ico
```

***

widget 为一个可视化窗口的类库

```C++
QT += core gui   // QT 的类库是以模块的形式添加
QT += widgets
QT += sql  // 使用数据库 SQL 
```

## chapter 3. 操作

信号与槽

QApplication 类（标准应用程序）对于 widget 程序必须要有

Q_OBJECT 宏，要使用信号与槽，就必须加入这个宏

使用代码，而不使用 ui 界面

使用代码方式可能实现所有功能，但是效率偏低；使用 ui 界面方便，但是有些功能无法实现。

创建 Label 并显示

```C++
#include <QLabel>
Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
    QLabel *label = new QLabel(this);
    label->setText("I love you.");
    setFixedSize(400,300);  //  == this -> setFixedSize(400,300)
    setWindowTitle("title");
    label->move(110,110);
}
```

创建 Button

```C++
#include <QPushButton>
	QPushButton *button = new QPushButton(this);
    button->setText("press");
    button->move(110,150);
```



- 一个信号与一个槽关联

```C++
connect(ui->pushButton,&QPushButton::clicked,this,&Widget::close);
// 信号：&QPushButton::clicked  槽：&Widget::close

// or
connect(mc1,&myclass::mysignal,mc2,&myclass::myslots);
```

- 一个信号触发另一个信号，另一个信号再触发它的槽

```
connect(ui->pushButton,&QPushButton::clicked,mc1,&myclass::mysignal);
```

- 一个信号关联多个槽

```C++
connect(mc1,&myclass::mysignal,mc2,&myclass::myslots);
connect(mc1,&myclass::mysignal,mc2,&myclass::myslots2);
```







***

- 对象树 QObject Trees

使用基类派生了一个新的类，并且指定该类属于窗体中的一部分，如果基类的窗体关闭的话，那么在它的窗体之上所创建出来的其他的派生类也会被自动销毁。

即基类窗体关闭，窗体之上所创建出来的派生类对象，会自动调用析构函数。因此，只需要 new，而不用自己在析构函数中 delete

QWidget 是 Qt Widgets module 的基础类，扩展了父子关系。一个子对象，即子窗体，显示在父类的坐标系统中



***

QDebug 用于打印



***

**QMainWindow** 

由五个部分组成：**Menu Bar，Tool Bar，Dock widget，Central widget，Status bar**

![](D:\Administrator\Pictures\forTypora\20230905.png)



**MenuBar** 菜单栏 只有一个，而 **ToolBar** 工具栏 可以有多个

```C++
#include <QMenuBar>
#include <QToolBar>
// 实现 MenuBar
QMenuBar *menubar = new QMenuBar(this);
setMenuBar(menubar);  // 因为只有一个 MenuBar 故为 set，否则使用 add
QMenu *filename = menubar->addMenu("File(&F)");  // alt+F
filename->addAction("Create new file(&C)");
filename->addAction("Open new file(&O)");


// 实现 ToolBar
QToolBar *toolbar1 = new QToolBar(this);
addToolBar(Qt::TopToolBarArea,toolbar1);
toolbar1->addAction("open");
toolbar1->addSeparator();  // 在当前位置添加分隔线
toolbar1->addAction("close");
```

状态栏 **StatusBar**

```C++
#include <QStatusBar>
QStatusBar *statusbar = new QStatusBar(this);
setStatusBar(statusbar);  // 只有一个 StatusBar
Qlabel *label = new QLabel(this);
label->setText("statusbar");   // 底部状态栏名称
statusbar->addWidget(label);
```

**DockWidget** 浮动窗口 

```C++
#include <QDockWidget>
QDockWidget *dockwidget = new QDockWidget("dockwidget",this);  // 名称 dockwidget
addDockWidget(Qt::LeftDockWidgetArea, dockwidget);  // 位置 Left 相对于中心部件 CentralWidget 而言

```

**CentralWidget**  中心部件，通常都是编辑界面，因为名为 TextEdit

```C++
#include <QTextEdit>
QTextEdit *textedit = new QTextEdit(this);
setCentralWidget(textedit);
```

***

ui 界面实现 QMainWindow

代码中为 Action 添加 icon



Qt 中使用资源文件格式：   

> ": + /前缀 /+ 文件名"

```C++
ui->actionOpen->setIcon(QIcon(":/image/icons/open.png"));
//  ：+前缀+文件名
```



***

## chapter 4 模态与非模态，标准对话框

模态（弹出对话框后，其他的功能不能使用，需要先关闭该对话框）与非模态对话框（弹出对话框后，其他的功能还能使用）



八种自带的标准对话框

**颜色对话框，文件对话框，字体对话框，输入对话框，消息对话框，进度对话框，错误信息对话框，向导对话框**

1. 非模态对话框

   ```C++
   #include <QDialog>
   QDialog *dialog = new QDialog(this);
   dialog->resize(200,100);
   dialog->show();
   ```

2. 模态对话框

   ```C++
   #include <QDialog>  // 不常用该方法
   QDialog *dialog = new QDialog(this);
   dialog->resize(200,100);
   dialg->exec();  // 模态对话框，是一个阻塞函数，不把对话框关闭，则不会出现主界面
   
   // 方式二：常用该方式
   dialog->setModal(true);  // 模态对话框
   dialog->show();
   dialog->setAttribute(Qt::WA_DeleteOnClose);  // 关闭小窗体时 delete 开辟出的空间，防止内存泄漏
   ```

***

只有主窗体关闭时候，程序中 new 开辟的内存空间才会被释放。

如果无限循环开辟对话框，那么会一直使用 new 开辟内存，会出现内存泄漏，内存不足。

理想情况时，关闭对话框时，释放之前 new 开辟的空间，而不需要等到关闭主界面

```C++
dialog->setAttribute(Qt::WA_DeleteOnClose);
```

### section 4.1 八大标准对话框 dialog

1. **颜色对话框**，使用**静态成员函数**（static public members）实现

```C++
#include <QColorDialog>
QColor color = QColorDialog::getColor(Qt::red,this);
```

2. **文件对话框**

```C++
#include <QFileDialog>
QString filename = QFileDialog::getOpenFileName(this,"Open File","/home/zhihongzeng/","Text Files (*.txt)");
qDebug()<<"file name: "<<filename;
```

3. **字体对话框**

```C++
#include <QFontDialog>
bool is_OK;
QFont fontname = QFontDialog::getFont(&is_OK,this;
if(is_OK)
	ui->pushButton->setFont(fontname);
else 
	qDebug()<<"font dialog error";
```

4. **输入对话框**

```C++
#include <QInputDialog>
bool is_OK;
// 输入文本
QString inputString = QInputDialog::getText(this,"dialog title","dialog label",QLineEdit::Normal,"default value",&is_OK);
	if(is_OK)
		qDebug()<<"the input:"<<iputString;
	else
    	qDebug()<<"input dialog error.";
// 输入整数
int inputInt = QInputDialog::getInt(this,"dialog title","dialog label",0,0,100,1,&is_OK);
    if(is_OK)
        qDebug()<<"the input integer: "<<inputInt;
    else
        qDebug()<<"input dialog error.";
```

5. **消息对话框**

```C++
#include <QMessageBox>
// 问题对话框
int ret = QMessageBox::question(this,"title","is it a question dialog?",QMessageBox::Yes,QMessageBox::No);
if(QMessageBox::Yes == ret)
    qDebug()<<"Yes";
else qDebug()<<"No";
//提示对话框
QMessageBox::information(this,"title","this is the information",QMessageBox::Ok);
// 警告对话框
QMessageBox::warning(this,"title","this is the warning",QMessageBox::Ok);
// 错误对话框
QMessageBox::critical(this,"title","this is the error",QMessageBox::Ok);
// 关于对话框
QMessageBox::about(this,"title","this is about");
```

6. **进度条对话框**

```C++
#include <QProgressDialog>
QProgressDialog progdialog("copy file","cancel",0,50000,this);
// QProgressDialog *progressdialog = new QProgressDialog(this);

progdialog.setWindowTitle("copy file");
progdialog.setWindowModality(Qt::WindowModal);  // 设置为模态对话框，即运行时，其他功能不可用
progdialog.show();
for(int i=0;i<=50000;i++)
{
    progdialog.setValue(i);
    QCoreApplication::processEvents();  // 多线程相关，可加可不加
    if(progdialog.wasCanceled())
        break;
}
qDebug()<<"copy file finished.";
```

7. **错误信息对话框** (通常使用消息对话框来代替)

```C++
#include <QErrorMessage>
QErrorMessage *errordialog = new QErrorMessage(this);
errordialog->setWindowTitle("error title");
errordialog->showMessage("this is the error message.");
errordialog->exec();  // 阻塞在此，不点击操作的话会一直存在

// 针对 取消勾选 show this message again 下次还是会出现同样的错误对话框，解决方法
QErrorMessage *errordialog = new QErrorMessage(this);  // 放在 mainwindow.h 的 private 中定义
```



8. **向导对话框**

```C++
#include <QWizard>

QWizard wizard(this);
wizard.setWindowTitle("wizard dialog title");
wizard.addPage(createPage());
wizard.addPage(createPage2())
wizard.exec();  // 阻塞在此，不点击操作的话会一直存在

// 所用到的显示的向导页 1
QWizardPage *createPage(void)
{
    QWizardPage *page = new QWizardPage;
    page->setTitle("Page 1");
    return page;
}
// 所用到的显示的向导页 2
QWizardPage *createPage(void)
{
    QWizardPage *page = new QWizardPage;
    page->setTitle("Page 2");
    return page;
}
```

- 备注

==成员函数与静态成员函数==

静态成员函数：static + 函数定义，不与类的对象关联的，只能通过类名+对象解析运算符+静态成员函数来访问

```C++
QColor color = QColorDialog::getColor(Qt::red,this);
```



## chapter 5 常用组件

### section 5.1 按钮控件组 button group

Buttons 包含：只是常用前四个

- **PushButto**n，最常用

- **ToolButton**

  工具按钮，如播放音乐时候的播放，下一曲，上一曲，静音等工具按钮，多以图标的形式出现，qq的头像也是

  ```C++
  ui->ToolButton->setIcon(QIcon(":/image/image/demo.png"));
  ```

  

- **RadioButton**

  单选按钮，多个只能选择一个

  然而，

  1. 问题一：默认是一个都不选，可在代码中修改初始状态选择一个 RadioButton

  ```C++
  ui->radioButton_2->setChecked(true);  // 默认会选择 radioButton
  ```

  2. 问题二：创建了很多的 radioButton，但是只能选择一个，因此需要分组（每个组都可以有一次选择机会）

  方法：在 ui 界面加入 GrouBox，把同一组的选项拖拽到该 GroupBox 框内；然后再加入一个 GroupBox，把另一组的选项拖拽到该框内。

  3. 问题三：在选择了某个选项之后，实现对应的特定 Action

  方法： connect 为该 RadioButton 添加信号和槽函数

  ​			可以定义一个类来实现槽函数的定义；后期会使用 lambad 表达式，更加方便

- **CheckButton**

  可以实现多选

  默认按钮是正方形的，属性中有 tristate 勾选的话选择可以有三种状态

- Command Link Button

  

- Dialog Button Box



### section 5.2 Items Widgets (Item-Based)

ui 界面中有以下两类

1. Items Views (Model-Based) 适合处理大量数据，如数据库
   - List View, Tree View, Table View, Column View
2. Items Widgets (Item-Based) 适合处理少量的数据
   - List Widget, Tree Widget, Table Widget

***

1. **List Widget** 内容清单

   listWidget 类似一个小文本框

   ```C++
   QListWidgetItem *widgetitem = new QListWidgetItem("The seconed line");
   ui->listWidget->addItem("The first line.");
   ui->listWidget->addItem(widgetitem);
   widgetitem->setTexAlignment(Qt::AlignHCenter);  // widgetitem 居中显示
   
   QStringList stringlist;  // stringlist 为容器
   stringlist<<"First Line"<<"Second Line"<<"Third Line"<<"Forth Line";
   ui->listWidget_2->addItems(stringlist);   // 显示多行内容
   ```

   

2. **Tree Widget**

   treeWidget

   ```C++
   // STEP 1 -> 设置顶层的内容
   QStringList list;
   list<<"first column"<<"second column";
   ui->treeWidget->setHeaderLabels(list);
   // 也可以使用以下一行代替前三行
   ui->treeWidget->setHeaderLabels(QStringList()<<"first column"<<"second column");  
   			// QStringList()<<"first column"<<"second column" 创建了一个没有匿名的类的对象
   
   // STEP 2 -> 设置主内容
   QTreeWidgetItem *treewidgetitem = new QTreeWidgetItem(QStringList("first item"));
   ui->treeWidget->addTopLevelItem(treewidgetitem);
   QTreeWidgetItem *treewidgetitem2 = new QTreeWidgetItem(QStringList("second item"));
   ui->treeWidget->addTopLevelItem(treewidgetitem2);
   // STEP 2.2 -> 为内容添加 Icon
   treewidgetitem->setIcon(0,QIcon(":/image/image/folder,png"));
   treewidgetitem2->setIcon(0,QIcon(":/image/image/folder,png"));
   
   // STEP 3 -> 设置主内容下的子内容
   QTreeWidgetItem *children = new QTreeWidgetItem(QStringList()<<"child content in column 1"<<"child content in column 2");
   treewidgetitem->addChild(children);  // 添加第一行子内容
   QTreeWidgetItem *children2 = new QTreeWidgetItem(QStringList()<<"child content in column 1"<<"child content in column 2");
   treewidgetitem->addChild(children2);  // 添加第二行子内容
   
   ```

   

3. Table Widget

tableWidget 类似 excel 表格，有行有列

```C++
// 设置 行 列 数量
ui->tableWidget->setColumnCount(3);  // 设置列数量 3
ui->tableWidget->setRowCount(5);     // 设置行数量 5
// 设置 行 列 的 label
ui->tableWidget->setHorizontalHeaderLabels(QStringList()<<"first column label"<<"second column label"<<"third column label");
//ui->tableWidget->setVerticalHeaderLabels();

// 给 表格 添加内容
ui->tableWidget->setItem(0,0,QTableWidgetItem("item(0,0)")); // 添加了内容 "item(0,0)" 于 0行0列
ui->tableWidget->setItem(1,0,QTableWidgetItem(QString::number(100)));  // // 添加了内容 100 于 1行0列

  // 或者使用循环来添加内容
QStringList stringlist;
stringlist<<"one"<<"two"<<"three"<<"four"<<"five";
for (int row = 0; row<5; row++)
{
    int column = 0;
    ui->tableWidget->setItem(row,column++,new QTableWidgetItem(stringlist[row]));  // 第一列内容填充
    ui->tableWidget->setItem(row,column,new QTableWidgetItem(stringlist[row]));    // 第二列内容填充
    // 后续列内容填充也是类似
}
```

***

### section 5.3 Containers 控件

containers 控件分为 

==Scorll Area, Tool Box, Tab Widget, Stacked Widget,== Frame, Widget, MDI Area, Dock Widget, QAxWidget

1. Scorll Area 滚动区域，即可以使用鼠标进行上下滚动显示的区域

   ![](D:\Administrator\Pictures\forTypora\2023090603.png)

2. Tool Box：类似 QQ 里面 "我的好友", "陌生人"等分组，点击以下，会展开显示

   ![](D:\Administrator\Pictures\forTypora\2023090602.png)

3. Tab Widget:  类似 Keil 的 target 选项卡

![](D:\Administrator\Pictures\forTypora\20230906.png)



4. Stacked Widget：栈窗体，可以把其他窗体放入，切换窗体







***

==lambda 表达式，快速实现信号与槽，而不用创建一个新的类==

```C++
[](){}
```

[] 存放要捕获的列表，如 [&] 捕获当前函数体内所有的变量，以引用的方式来使用

​									  [=] 捕获当前函数体内所有的变量，使用的是它们的副本

() 参数列表

{} 函数体

使用 lambda表达式实现信号的槽函数

```C++
connect(ui->pushButton,&QPushButton::clicked,this,[](){ qDebug()<<"Button clicked."; })
```

***





### section 5.4 Input Widget 控件

inputWidget 包含：

==Combo Box,== Font Combo Box, Line Edit, ==Text Edit,== Plain Text Edit, Spin Box, Double Spin Box, Time Edit, Date Edit,

Date/Time Edit, Dial, Horizontal Scroll Bar, Vertical Scroll Bar, ==Horizontal Slider, Vertical Slider, Key== Sequence Edit 



1.  Combo Box

![](D:\Administrator\Pictures\forTypora\2023090604.png)

2. Font Combo Box  选择字体

   通过 Font Combx Box 选择字体，按下 pushButton 后修改 label 的字体为当前所选择的。

```C++
connect(ui->pushButton,QPushButton::clicked,this,[&](){ui->label-setFont(ui->fontComboBox->currentFont());});
```

![](D:\Administrator\Pictures\forTypora\2023090605.png)

3. Line Edit  输入一行文本



4. Text Edit 与 Plain Text Edit

   都是多行文本，Text Edit 具有更多功能，

![](D:\Administrator\Pictures\forTypora\2023090606.jpg)

5. Spin Box 与 Double Spin Box

   数值细调，整数与小数

![](D:\Administrator\Pictures\forTypora\2023090607.png)

6. Time Edit，Date Edit 与 Date/Time Edit

   设置时间，日期，日期与时间

![](D:\Administrator\Pictures\forTypora\2023090608.png)

​		设置为系统时间

​		

```C++
QDateTime curDateTime = QDateTime::currentDateTime();
ui->timeEdit->setTime(curDateTime.time);  // 对 Time Edit 设置时间
ui->dateEdit->setDate(curDateTime.date);  // 对 Date Edit 设置日期
// 对 Date/Time Edit 设置时间和日期
ui->dateTimeEdit->setTime(curDateTime.time);
ui->dateTimeEdit->setDate(curDateTime.date);
```

![](D:\Administrator\Pictures\forTypora\2023090609.png)

7. Dial

   一个旋钮，通过旋转来改变数值

![](D:\Administrator\Pictures\forTypora\2023090610.png)



8. Horizontal Scroll Bar 与 Vertical Scroll Bar

   水平滑动条，与 垂直滑动条

![](D:\Administrator\Pictures\forTypora\2023090611.png)

9.  Horizontal Slider 与 Vertical Slider

   ==水平滑块，与 垂直滑块。通常用于粗调数值，可与 Spin Box 与 Double Spin Box（数值细调） 配合使用；==

   实现移动滑块，相应的值会改变，并显示出来；同理，Spin Box 的值也会在 滑块中体现出来。

![](D:\Administrator\Pictures\forTypora\2023090612.png)

10. Key Sequence Edit 

    并不常用，pass

### section 5.5 自定义控件

 自定义一个控件：Spin Box （细调）与 Horizontal Slider（粗调） 组合

- Step 1 -> 新建一个 Qt 设计师界面类（my_diy），在 my_diy.ui 中添加 Spin Box 和 Horizontal Slider

- Step 2 -> 在 widget.ui 中添加一个 Contioners 中的 Widget

- Step 3 -> 在 my_diy.ui 中选中diy的控件，右键提升为，并勾选全局包含。（这样子，diy 的控件就在widget 中可见了）

- Step 4 -> 在 my_diy.cpp 中对 diy 的控件进行操作（关联它们之间的数值）

```C++
void (QSpinBox::*ptrFunc)(int) = &QSpinBox::valueChanged;  // 函数指针 ptrFunc 指向的函数是  &QSpinBox::valueChanged(int)
// 因为，具有两个  &QSpinBox::valueChanged 函数（即发生了函数重载），为了指定要使用的函数，使用函数指针

connect(ui->spinBox,ptrFunc,ui->horizontalSlider,&QSlider::setValue);
// 实现 horizontalSlider 的值改变时，SpingBox 的滑块也相应的移动

connect(ui->horizontalSlider,&QSlider::valueChanged,ui->spinBox,&QSpinBox::setValue);
// 实现 SpingBox 滑块的移动时，horizontalSlider 的值也相应的改变
```

- Step 5 -> 实现该 diy 控件与 widget 中其他控件交互

如，通过 widget 中的按键来访问该控件，按下按键返回当前的数值

```C++
connect(ui->pushButton,&QPushButton::clicked,this,[&](){ qDebug()<<"pressed and current value:"<<ui->widget->getCurVal();});
// 其中 getCurVal() 是 my_diy 类的一个公有成员函数，定义为
int getCurVal(void)
{
    return ui->spinBox->value();  // 此处的 ui 是指 my_diy.ui 中的 widget，而不是整个 widget
}
```



***

### section 5.6  Labl 控件

label 控件常用以下三个功能：显示文本，显示图片，显示动图

- 功能 1 -> 显示文本内容

```C++
ui->label->setText("Hello Usman.");
```

- 功能 2 -> 显示图片

```C++
// 先往项目中添加 image 资源文件，添加图片 rocket.jpeg 进去

ui->label->setPixmap(QPixmap(":/image/image/rocket.jpeg"));  // 显示图片
ui->label->setScaledContents(true);  // 设置缩放，以适应 label 窗口的大小
```

- 功能 3 -> 显示动图 .gif

```C++
// 添加图片 duck.gif 进去image 资源文件
#include <QMovie>
QMovie *mv = new QMovie(":/image/image/duck.gif");
ui->label->setMovie(mv);
ui->label->setScaledContents(true);  // 设置缩放，以适应 label 窗口的大小
mv->start();
```



***

### section 5.7 鼠标事件

鼠标键的按下，鼠标指针的移动，都属于事件

事件的处理流程如下：（自动完成的，靠的是大量回调函数）

1. 事件触发（产生事件）

   事件产生后，会使用 QApplication 中的函数 notify() 将事件发送到指定的窗体

2. 事件过滤

   QObject 中具有事件过滤器 eventFilter() 函数

3. 事件分发

   QWidget 中有各种事件 Events，事件分发器对事件进行分发，如鼠标事件，绘图事件，键盘事件；

   并自动找到事件处理函数，如鼠标事件，找到 mousePressEvent() 或 mouseMoveEvent() 等函数中的一个，并分发出去

4. 事件处理

   调用相应的事件处理函数（类型是 virtual protected 虚保护函数，都是回调函数）

   备注：==虚函数：函数只是定义，但是具体要实现什么，在用到该函数时需要自己重新定义==

   ​		  ==回调函数：只需要自己实现函数功能，不用自己去调用，函数是如何调用的不归我们管（但某个事件触发时，会自动调用相应的事件处理函数，靠				 的是回调函数）==

main 函数中

```C++
int main()
{
	QApplication a;
	return a.exec();  // 自动自动检测该窗体的事件
}
```

***

- Step 1 -> 在 widget.h 中重新声明 mousePressEvent()

```C++
protected:
	void mousePressEvent(QMoseEvent *event);
```

- Step 2 -> 在 widget.cpp 中定义该函数

```C++
#include <QMouseEvent>
void Widget::mousePressEvent(QMoseEvent *event)
{
// Qt::LeftButton
// Qt:RightButton
	if( Qt::LeftButton == event->button())
    {
        qDebug()<<"left button clicked";
        qDebut()<<"position: x="<<event->x()<<" y="<<event->y();   // x(), y() 相对于 widget 而言
        														   // globalX(), globalY() 相对于整个屏幕而言
    }
    else if(Qt::RightButton == event->button())
    {
        qDebug()<<"right button clicked";
    }   
    else qDebug()<<"unknown button clicked";
}
```

- 功能 2：设置鼠标左键双击全屏，或退出全屏

```C++
// widget.h 中
protected:
	void mouseDoubleClickEvent(QMoseEvent *event);
// widget.cpp 中	
void Widget::mouseDoubleClickEvent(QMoseEvent *event)
{
	if( Qt::LeftButton == event->button())
    {
        qDebug()<<"left button double clicked";
		if(windowState() != Qt::WindowFullScreen)
            setWindowState(Qt::WindowFullScreen);
        else setWindowState(Qt::WindowNoState);  // 恢复原始状态
    }
}
// 备注：窗口状态在 QWidget::windowState() 中
```

-  功能 3：鼠标指针移动事件，左键按住并移动，使得窗口移动

```c++
// widget.h 中
private:
	QPoint pos;
protected:
	void mouseMoveEvent(QMoseEvent *event);
// widget.cpp 中	
void Widget::mouseMoveEvent(QMoseEvent *event)
{
	if(Qt::LeftButton && event->buttons())  // 使用 buttons()，因为是多个动作（按下左键，移动）
    {
        QPoint temp;
        temp = event->globalPos - pos;  // 其中 pos 在 widget.h 中定义
        move(temp);
    }
}
```

-  功能 4：鼠标滚轮事件，实现 zoom in 和 zoom out 放大缩小操作，对 TextEdit 中的字体

```C++
// widget.h 中
protected:
	void wheelEvent(QWheelEvent *event);
// widget.cpp 中	
void Widget::wheelEvent(QWheelEvent *event)
{
	if(event->delta() > 0)  
        ui->textEdit->zoomIn();  // 向上滚动  放大
    else ui->textEdit->zoomOut();  // 向下滚动  缩小
}
```



***

### section 5.8  定时器

使用定时器，定时地改变 label 里面的内容

1. 方法一 使用 timer 加 connect

```C++
#include <QTimer>

QTimer *timer = new QTimer(this);
timer->start(1000);  // every 1s
connect(timer,&QTimer::timerout,[&](){ static int cnt =0; ui->label->setText(QString::number(cnt++))} )

```

2. 方法二  使用 timer 加 事件处理函数

```C++
// widget.h 中
protected:
	void timerEvent(QTimerEvent *event);
// widget.cpp 中	
#include <QTimerEvent>
void Widget::wheelEvent(QWheelEvent *event)
{
	static cnt = 0;
    ui->lable_2->setText(QString::number(cnt++));
}
// 使用
startTimer(500);  // every 500ms
```

***

方法一很容易再定义一个定时器，而方法二存在问题，如何使用第二个定时器呢？

==解决办法：利用 startTimer() 的返回值，返回值是定时器的标识 ID 为 int 类型==

```C++
// widget.h 中
private:
	int timerID1, timerID2;
// widget.cpp 中	
#include <QTimerEvent>
void Widget::wheelEvent(QWheelEvent *event)
{
	static int cnt = 0;
    static int cnt2 = 0;
    if(timerID1 == event->timerId())
        ui->lable_2->setText(QString::number(cnt++));
    else if(timerID2 == event->timerId())
        ui->lable_3->setText(QString::number(cnt2++));
}

timerID1 = startTimer(500);  // every 500ms
timerID2 = startTimer(1000);  // every 1s
```

==当需要使用多个定时器时，方法一更加方便==



***

### section 5.9 事件分发

==大部分时候，自动使用回调函数实现事件的分发，自动回调事件处理函数==

极少情况下，用户需要拦截该事件，意味着需要特殊处理该事件。==不推荐使用，因为则会导致默认的回调函数不起作用==

QWidget 中有 event() 函数

```C++
// widget.h 中
public:
	bool event(QEvent *event);
// widget.cpp 中	
bool Widget::event(QEvent *event)
{
	if(event->type() == Qt::MouseButtonPress)  // 特殊事件处理
    {
        qDebug()<<"mouse pressed.";
    	return true; 
    }
     return QWidget::event();  // 其他事件，还是使用 父类的事件分发器，回调默认的事件处理函数  
}
```







以上即为 Qt 基础内容

***

## chapter 6 串口调试助手项目







***

### section 6.1 自动识别串口号

使用定时器，定时 500ms 

扫描串口

```C++
// widget.h 中
private:
	QTimer *timerID1;
// widget.cpp 中	
#include <QtSerialPort/QtSerialPort>
bool Widget::event(QEvent *event)
{
	QStringList newPortStringList;
    static QStringList prePortStringList;
    newPortStringList.clear();
    foreach(const QSerialPortInfo &info, QSerialPortInfo::availablePorts)
        newPortStringList += info.portName;
    if(prePortStringList != newPortStringList)
    {
        ui->comboBox->clear();
        ui->comboBox->addItems(newPortStringList);
        prePortStringList = newPortStringList;
    }
}
```



***

### section 6.2 设置串口参数





***

### section 6.3 接收功能 









***

### section 6.4 发送功能



```C++
QByteArray byteArray;  // 用于发送 write 要发送 QByteArray 类型
sendText = ui->textEdit_2->toPlainText();
byteArray = sendText.toLation1();  // 将 QString 类型的 sendText 转换为 QByteArray 类型
sendText_byte += sendText.length();
ui->label_12->setText(QString::number(sendText_byte));
mySerial->write(byteArray);
```







***

## chapter 7 绘图基本操作

widget一运行就会触发绘图事件，绘图事件由 QPainter 中的 paintEvent(QPaintEvent *) 处理函数自动调用



```C++
// 首先，在 widget.h 的public 中增加 void paintEvent(QPaintEvent *)
#include <QPainter>
// void paintEvent(QPaintEvent *) 定义在 widget.cpp 中，如下
// 使用画笔 QcPen
QPainter painter(this);
QPen myPen(QClolor(255,0,0));
myPen.setWidth(3);
myPen.setStyle(Qt::DashDotLine);
painter.setPen(myPen);
painter.drawLine(QPoint(0,0),QPoint(100,100));

// 使用画刷 QBrush
QBrush myBrush(Qt::green,Qt::SolidPatten);
painter.setBrush(myBrush);
painter.setPen(myPen);
painter.drawRect(200,200,50,50);

//使用渐变色
QConicalGradient conicalGradient(QPointF(300,300),0);  // 从 0 度开始，逆时针
conicalGradient.setColorAt(0.2,Qt::gray);  // 0 ~ 0.2*360 度，渐变为 灰色
conicalGradient.setColorAt(0.5,Qt::green);
conicalGradient.setColorAt(0.7,Qt::blue);  
conicalGradient.setColorAt(1,Qt::yellow);   // 0.7*360  ~ 360 度，渐变为  黄色
```

***

```C++
QPainter painter(this);
QPen myPen(Qt::red);
myPen.setWidth(15);
painter.setPen(myPen);   //  == painter.setPen(QPen(Qt::red,15));  创建一个匿名对象
```



### section 7.1 抗锯齿和坐标系变换

1. ==抗锯齿 setRendeHints， 坐标系平移 translate==

```C++
QPinter painter(this);
QPen myPen(Qt::red);
myPen.setWidth(15);
painter.setPen(myPen);
painter.drawEllipse(QPoint(200,200),100,100);

painter.setRendeHints(QPainter::Antialiasing);  // 对第二个圆使用 抗锯齿 操作，使得表面平滑
painter.translate(200,0);  // 坐标系平移，x 向右平移 200，y 不变，取负值是向左平移
painter.drawEllipse(QPoint(200,200),100,100);
```

2. ==坐标系旋转 rotate，使用时，一般先平移，在旋转坐标系==

```C++
painter.save();  // 对坐标系进行操作之前，先保存当前的坐标系
painter.setPen(QPen(Qt::green,10));
painter.drawLine(QPoint(20,20),QPoint(100,100));
painter.rotate(90);  // 顺时针旋转 90 度
painter.setPen(QPen(Qt::blue,10));
painter.drawLine(QPoint(20,20),QPoint(100,100));

painter.restore();  // 恢复原来坐标系
```

3. ==坐标系缩放 scale==

```C++
painter.save();  // 对坐标系进行操作之前，先保存当前的坐标系
painter.setPen(QPen(Qt::green,5));
painter.setBrush(Qt::red);
painter.drawRect(300,300,50,50);
painter.scale(1.5,0.5);  // x 放大 1.5 倍， y放大 0.5 倍
painter.setBrush(Qt::green);
painter.drawRect(300,300,50,50);

painter.restore();  // 恢复原来坐标系
```

4. ==坐标系扭曲 shear（不推荐使用）==

```C++
painter.save();  // 对坐标系进行操作之前，先保存当前的坐标系
painter.setPen(QPen(Qt::green,5));
painter.setBrush(Qt::red);
painter.drawRect(300,300,50,50);
painter.shear(0.5,0)
painter.setBrush(Qt::black);
painter.drawRect(300,300,50,50);  // 扭曲后坐标变为 （450，300，50，50）变成平行四边形，每一个点的 x 坐标变成 1.5 倍
painter.restore();  // 恢复原来坐标系
```



### section 7.2 手动调用绘图事件处理函数

widget一运行就会自动触发绘图事件  paintEvent(QPaintEvent *) ，如何刷新绘图，如钟表，动画？

解决方法：手动调用 repaint() 或 update() 推荐使用 update()，会自动回调  paintEvent(QPaintEvent *) 

```C++
QTimer *timerID1 = new QTimer(this);
timerID1->start(1000);
connect(timerID1, &QTimer::timeout,this,[&](){ update(); });  // 每隔 1s 调用 paintEvent 绘图

```





### section 7.3 绘制文字和路径  path 为一个容器

```C++
QPainter painter(this);
QPainterPath path;
path.moveTo(50,250);
path.moveTo(50,200);
path.lineTo(100,100);
pathe.addEllipse(QPoint(100,100),30,30);
painter.setPen(Qt::red);
painter.drawPath(path);
path.translate(200,0);  // 坐标轴先 x 方向右平移 200
painter.setPen(Qt::blue);
painter.drawPath(path);  // 使用 path 容器，绘图
```





### section 7.4 绘图设备_QPixmap_QImage

Qt 中绘图工具，QWidget，QPixmap，QImage，QBitmap，QPicture

==QPixmap：图片类，主要用于显示图片，对于图片的显示做了优化处理，和平台有关，只能在主线程中==

==QImage：图片类，图依赖于平台的，多用于图片的传输，可以做像素级修改，可在主线程，多线程中==

```C++
// 不需要paintEvent(QPaintEvent *)
#include <QPainter>
#include <QPen>
#include <QPixmap>
#include <QImage>

// 使用 QPixmap
QPixmap pix(400,300);  // 画布大小 400*300
pix.fill(Qt::white);   // 画布全白
QPainter painter(&pix);
painter.setPen(QPen(Qt::red,5));
painter.setRenderHints(QPainter::Antialiasing);
painter.drawEllipse(QPoint(100,100),50,50);
pix.save("D://mypix.jpg");

// 使用 QImage
QImage img(400,300);   // 画布大小 400*300
img.fill(Qt::white);   // 画布全白
QPainter painter2(&img);
painter2.setPen(QPen(Qt::green,5));
painter2.setRenderHints(QPainter::Antialiasing);
painter2.drawEllipse(QPoint(100,100),50,50);
pix.save("D://myImg.jpg");
```

==使用 QImage 来修改图片中的像素点==

```C++
QPainter painter(this);
QImage img;
img.load(":/image/image/demo.jpeg");
for(int i=0;i<50;i++)
{
    for(int j=0;j<100;j++)
    {
        QRgb rgb = qRgb(255,0,0);
        img.setPixel(i,j,rgb);
    }
}
painter.drawImage(0,0,img);

```



### section 7.5 绘图设备_QPicture_QBitmap



==QPicture：可视为一个绘图的容器，里面保存有绘图的记录和重绘制的命令，存储的形式是二进制形式，不方便打开(要用 Qt 代码打开显示)==

==QBitmap：黑白图片==

1. 使用 QPicture

```C++
QPicture myPicture;
QPainter painter(&myPicture);  // == QPainter painter; painter.begin(&myPicture);
painte.setPen(QPen(Qt::red,5));
painter.drawEllipse(QPoint(100,100),50,50);
painter.end();
myPicture.save("D://picture.jpg");  // 任意后缀文件
```

想要显示该图片，如

```C++
QPaint painter(this);
QPicture mypic;
mypic.load("D://picture.jpg");
painter.drawPicture(0,0,mypic);
```

2. 使用 QBitmap

```C++
QBitmap mybitmap(400,300);
QPainter painter2(&mybitmap);
painter2.setPen(QPen(Qt::green,5));
painter2.drawEllipse(QPoint(100,100),50,50);
mybitmap.save("D://bitmap.jpg");   // 图片为黑白的，并且会有很多不正常的像素点
```

使用 QBitmap打开图片，会自动转换为黑白图片，但是会出现很多不正常的像素点

```C++
QPaint painter(this);
QBitmap mybitmap;
mybitmap.load("D://demo.jpeg");
painter.drawPixmap(0,0,mybitmap);
```



### section 7.6 绘制弧线



```C++
QPaint painter(this);
QPen mypen;
mypen.setColor(Qt::red);
my.setWidth(5);
painter.setPen(mypen);
painter.setRenderHints(QPainter::Antialiasing);
painter.drawArc(25,25,1550,1550,0,180*16);  // 在 （0，0）开始 长 1550，宽1550的矩形中，画弧线，起始角度0，结束角度 180度
```





## chapter 8 超声波雷达项目



section 8.1 雷达主界面



section 8.2 绘制扫描效果





section 8.3 Qt 串口接收 STM32 发送的数据



section 8.4 项目完成





## chapter 9 文件相关操作



### section 9.1 文件读写



```C++
#include <QFileDialog>
#include <QFile>   // 操作文件
connect(ui->pushButton,&QPushButton::clicked,[&]()  // & 表示以引用的形式
        {
            QString fileName = QFileDialog::getOpenFileName(this,"open file","E:\\Zhz2023\\Images");
            ui->lineEdit->setText(fileName);
            QFile file(fileName);
            file.open(QIODevice::ReadOnly);  // 打开文件，读操作
            QByteArray fileArray = file.readAll();  // 读操作
            /*
            QByteArray array;
            while(!file.atEnd())
            {
            	array += file.readline();
            }
            */
            ui->textEdit->setText(fileArray);  // 显示读取的文本内容
            file.close();
            file.open(QIODevice::Append);  // 打开文件，写操作
            file.write("\nThe line add.");
            file.close();
        });
// 或者一行一行读
QByteArray fileArray;
while(!file.atEnd)
{
    fileArray += file.readLine();  // 逐行读取文本
}
 
```



### section 9.2 文件信息和文件定位操作



```c++
QFile file("/home/zhihongzeng/QtFile");
QFileInfo info(file);
qDebug()<<"绝对路径："<<info.absoluteFilePath();
qDebug()<<"文件名称："<<info.fileName();
qDebug()<<"文件后缀："<<info.suffix();
qDebug()<<"创建时间："<<info.created();
qDebug()<<"文件大小："<<info.size();

file.open(QIODevice::ReadOnly);
file.seek(0);
QByteArray array = file.read(5);  // 前五个字节
qDebug()<<"前五个字节"<<array;
qDebug()<<"当前文件位置"<<file.pos();

file.seek(10);
array = file.read(5);  // 前五个字节
qDebug()<<"10-15个字节"<<array;
file.close();
```

### section 9.3 目录操作_文件系统监视器

```c++
#include <QDir>

QDir myDir("/home/zhihongzeng/QtFile");
ui->listWidget->addItem(myDir.absolutePath());
ui->listWidget->addItems(myDir.entryList());
myDir.setNameFilters(QStringList("*.cpp"));
ui->listWidget->addItems(myDir.entryList());

myDir.mkdir("QtMydir");
myWatcher.addPath("/home/zhihongzeng/QtFile/QtMydir");
myWatcher.addPath("/home/zhihongzeng/QtFile/demo.cpp");
connect(&myWatcher,&QFileSystemWatcher::directoryChanged,this,&Widget::showMess);
connect(&myWatcher,&QFileSystemWatcher::fileChanged,this,&Widget::showMess);


// WiDget.h
#include <QFileSystemWatcher>
private:
    Ui::Widget *ui;
    QFileSystemWatcher myWatcher;
private slots:
    void showMess(const QString &path);
```



### section 9.4 文本流和数据流



```c++
#include <QFileDialog>
#include <QFile>   // 操作文件
#inlcude <QTextStream>
connect(ui->pushButton,&QPushButton::clicked,[&]()  // & 表示以引用的形式
        {
            QString fileName = QFileDialog::getOpenFileName(this,"open file","E:\\Zhz2023\\Images");
            ui->lineEdit->setText(fileName);
            QFile file(fileName);
            file.open(QIODevice::ReadOnly);  // 打开文件，读操作
            
            QTextStream stream(&file);
            stream.setCodec("utf-8");
            QByteArray array;
            while(!stream.atEnd())
            {
            	array += stream.readline();
            }
            
            ui->textEdit->setText(array);  // 显示读取的文本内容
            file.close();
  
        });
QFile file(/home/zhihongzeng/QtFile/QtMydir/test.txt);
file.open(QIODevice::WriteOnly);
QTextStream date_stream(&file);
data_stream<<QString("hi Qt.")<<(qint32)65;
file.close();

file.open(QIODevice::ReadOnly);
QTextStream date_in_stream(&file);
QString str;
qint32 n;
data_in_stream>>str>>n;
qDebug()<<"str:"<<str"  ,n:"<<n;
file.close();

//  传输图片，后面待完善，通过 TCP/UDP 传输
QImage image("/flower.jpg");
QByteArray arr;
QTextStream date_stream(&arr,QIODevice::ReadWrite);
date_stream<<image;

```



### section 9.5 播放音频文件

```c++
Qt += multimedia;

#include <QMediaPlayer>
QMediaPlayer *player;  //.h文件中

player = new QMediaPlayer(this);
player->setMedia(QUrl::fromLocalFile("../Audio/music.mps"));
player->setVolume(50);
connect(&QPushButton);  // 通过按键控制播放，待完成
player->play();  // 播放
player->stop();  // 停止

```

按键音，开机音，一般都是 .wav 形式无损音质，.mps 是压缩格式

```c++
#include <QSoundEffect>
QSoundEffect *effect; 

effect = new QSoundEffect(this);
effect->setSource(QUrl::fromLocalFile("../Audio/music2.wav"));
effect->setVolume(0.5);
effect->play(); 
effect->stop();
```



### section 9.6 播放视频文件





### section 9.7 摄像头





## chapter 10 Qt 数据库

### section 10.1 创建数据库

依靠的是 SQL 模块

| 驱动        | 数据库                                |
| ----------- | ------------------------------------- |
| QDB2        | IBM DB2(7.1及更新)                    |
| QIBASE      | BOrland InterBase                     |
| QMYSQL      | MySQL                                 |
| QOCI        | Oracle Call Interface Driver          |
| QODBC       | Open Database Connectivity            |
| QPSQL       | PostgreSQL(7.3及更新)                 |
| QSQLITE2    | SQLite2                               |
| **QSQLITE** | **SQLite3**                           |
| QSYMSQL     | 针对 Symbian 平台的 SQLite3           |
| QTDS        | Sybase Adaptive Server(自 Qt4.7 废除) |

SQL 的分层

**数据库 -> 驱动层 -> SQL 接口层（QSQLDatabase）-> 用户接口层**（QSQL....Model 作用是将数据库链接到窗口部件）

用户使用的是接口层，驱动层是桥梁，

- 使用SQLite3，经常用于嵌入式

```c++
QT       += core gui sql   // 要使用数据库，添加 sql
    
#include <QSqlDatabase>
#include <QSqlError>  // 数据库错误相关
    
QStringList drivers = QSqlDatabase::drivers();  // 查看支持哪些数据库
foreach(QString d,drivers)
    qDebug()<<d;
// 创建数据库
    QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
	db.setDatabaseName("database.db");
	if(!db.open())
    {
        qDebug()<<"Failed to onnect."<<db.lastError();
    }
	else
    {
        qDebug()<<"Connect to database successfully.";
    }

```

**SQLite 数据库中有很多命令和指令 ，网上可以找到 sqlite 语句大全，学习数据库就是学习数据库指令，以及在Qt中调用这些命令**

### section 10.2 插入数据库_遍历数据库

数据库中分为不同的表格table，数据要存入 table 中

```c++
#include <QSQlQuery>  // 数据库命令


QSqlQuery sql_query;
// create a table student(int id, varchar name(30), int age )
QString create_sql = "create a table student( id int, name varchar(30), age int )"
sql_query.prepare(create_sql);
	if(!sql_query.exec()}
   {
       qDebug()<<"Failed to create table."<<sql_query.lastError();
   }
	else
   {
       qDebug()<<"Table created."
   }

   // 添加数据
   // "insert into student values( ?, ?, ?)"
   QString insert_sql = "insert into student values( ?, ?, ?)";
   sql_query.prepare(insert_sql);
   sql_query.addBindValue(1);  // 三个数据，因此要三次
   sql_query.addBindValue(“Rick”);
   sql_query.addBindValue(18);
   if(sql_query.exec())
   		qDebug() <<"Insert student no.1 OK";
   
   QString insert_sql = "insert into student values( ?, ?, ?)";
   sql_query.prepare(insert_sql);
   sql_query.addBindValue(2);  // 三个数据，因此要三次
   sql_query.addBindValue(“Lihua”);
   sql_query.addBindValue(19);
   if(sql_query.exec())
   		qDebug() <<"Insert student no.2 OK";
   
   // 显示数据库
   // "select * from student"
   QString select_all_sql = "select * from student";
   sql_query.prepare(select_all_sql);
   if(sql_query.exec())
   {
       qDebug() <<"show database:\n";
       while(sql_query.next())
       {
           int id = sql_query.value(0).toInt();
       	   QString name = sql_query.value(1).toString();
           int age = sql_query.value(2).toInt();
           qDebug() << "id: "<<id<<", name:"<<name<<", age:"<<age;
       }
   }
   		
```



### section 10.3 更新_删除数据库

```c++
// 删除数据库
“delete from student”
QString clear_sql = “delete from student”
sql_query.prepare(clear_sql);
if(!sql_query.exec())
   		qDebug() <<"failed to clear table.";
else
{
	qDebug() << "Table cleared";
}

//  更新数据库
"update student set name = :nm where id =n"
QString update_sql = "update student set name = :nm where id = :n";
sql_query.prepare(update_sql);
sql_query.bindValue(":nm","Michael");
sql_query.bindValue(":n","1");  //修改 id 为1的那一行 name 为 "Michael"
if(!sql_query.exec())
   		qDebug() <<"failed to update table.";
else
	qDebug() << "Table updated";

 // 删除某一行
“delete form student where id = n”
QString delete_sql = “delete form student where id = ?”;
sql_query.prepare(update_sql);
sql_query.bindValue("2");  // 删除 id 为2的那一行
if(!sql_query.exec())
   	qDebug() <<"failed to delete table row NO.2.";
else
	qDebug() << "Table row NO.2 deleted";
```





### section 10.4 数据库数据窗体访问，修改以及查找

```c++
QT       += core gui sql   // 要使用数据库，添加 sql
    
#include <QSqlDatabase>
#include <QSqlError>  // 数据库错误相关
#include <QSQlQuery>  // 数据库命令
```

以上是 库提供SQL 接口层，

**用户接口层（QSQL....Model 作用是将数据库链接到窗口部件）**

```c++
#include <QSqlQueryModel>
#include <QSqlTableModel>
#include <QSqlRelationModel>

#include <QTableView>  // ui 界面中的表格，用于显示数据库

QSqlTableModel *model;  // .h 中


// .c 中

model = new QSqlTableModel(this);
ui->tableView->setModel(model);
model->setTable(student);
model->select();
// 将 id，name，age 修改为中文，仅仅是显示改了
model->setHeaderData(0,Qt::Horizontal,"序号");
model->setHeaderData(1,Qt::Horizontal,"姓名");
model->setHeaderData(2,Qt::Horizontal,"年龄");


model->setEditStrategy(QSqlTableModel::OnManualSubmit);  // 修改后需要 submit 才会生效

connect(ui->pushButton1,&QPushButton::clicked(),this,[&](){model->submit()});  // 修改后点击按键提交修改
connect(ui->pushButton2,&QPushButton::clicked(),this,[&](){model->revertAll()});  // 撤销修改


// 查找

connect(ui->pushButton3,&QPushButton::clicked(),this,[&](){
    QString name = ui->lineEdit->text();
    QString nm = QString("name='%1' ").arg(name);
    // " ame = 'Tom' "
    model->setFilter(nm);  // 
    model->select()
}); 
```

QString nm = QString("name='%1' ").arg(name);

将字符串 name 内容替换掉 %1





## chapter 11 Qt 进程

### section 11.1 启动另一个进程 process

Qt 运行起来相当于一个进程

```c++
// .h 中
#include <QProcess>
QProcess myProcess;

// .c 文件
myProcess.start("/QT/5.6mingw49_32/bin/assistant.exe");  // 这样两个进程同时运行

// 运行，并使 Qt 阻塞
myProcess.waitForFinished();
```



### section 11.2 进程间通信

任务间的通信与同步 IPC：有很多方法

如：共享内存

一个进程往共享内存中写，宁一个往里面读

```c++
#include <QShareMemory>
QShareMemory sharedMemory; 
sharedMemory.setKey("My_Shared_Memory"); // 共享内存具有唯一的键值


// write
if(sharedMemory.isAttached()) //判断共享内存是否被其他绑定
{
    sharedMemory.detach();  // 如果有绑定，解除绑定
}
if(!sharedMemory.create(100,QSharedMemory::ReadWrite))
{
    qDebug()<<"failed to create shared memory.";
}
sharedMemory.lock();   // 对共享内存上锁，其他程序不能使用该内存
// 中间的叫 临界代码
QByteArray arr = ui->lineEdit->text().toLatin1; // toLatin1 返回 QByteArray 的数组
memcpy(sharedMemory.data(),arr.data(),(size_t)qMin(sharedMemory.size(),ui->lineEdit->text().size()));
// 往 sharedMemory.data() 这里面写
// 数据来源 arr.data() ，这两个都是指针
// qMin 是Qt自带的取小值函数

sharedMemory.unlock(); 
```

read sharedMemory 工程

```c++

if(!sharedMemory.attach())
{
    	qDebug()<<"attach shared memory failed.";
    return;
}
sharedMemory.lock(); 
char *arr= new char[sharedMemory.size()];
memcpy( arr,sharedMemory.data(),(size_t)sharedMemory.size() );
// 往 arr 这里面写
// 数据来源 sharedMemory.data() ，这两个都是指针
// 读取长度：sharedMemory.size()
ui->label->setText(QString(arr));
sharedMemory.unlock(); 
```



### section 11.3 多线程并发操作

Qt下多线程开发

```c++
//MyThread.h

#include <QThread>
class MyThread : public QThread
{
Q_OBJECT 
public:
	MyThread(){stopped = false;}
    void stop();
protected:
	void run();  // 线程从 run 函数开始运行，类似于 main 函数，run函数的调用靠的是 QThread 类中的start
private:
    bool stopped;
};

//MyThread.c
#include "mythread.h"
void MyThread::run()
{
    static int i = 0;
    while(!stopped )
    {
        qDebug() <<"MyThread:"<<i;
        msleep(1000);  //  ==QThread::msleep(1000); 
        i+=2;
    }
    stopped = false;
}
void MyThread::stop()
{
    stopped = true;
    
}

 //  widget.h
MyThread thread;
 //  widget.c

connect(ui->pushButton,&QPushButton::clicked(),this,[&](){  
thread.start();
ui->pushButton->setEnable(false);  // 按了一次启动线程之后，该按键不再起作用
ui->pushButton_2->setEnable(true);
}); 

connect(ui->pushButton_2,&QPushButton::clicked(),this,[&](){  
if(thread.isRunning())
{
    thread.stop();
    ui->pushButton->setEnable(true);
	ui->pushButton_2->setEnable(false);
}
  
}); 
```

另一个线程

```c++
//MyThread2.h

#include <QThread>
class MyThread2 : public QThread
{
Q_OBJECT 
public:
	MyThread2(){stopped = false;}
    void stop();
protected:
	void run();  // 线程从 run 函数开始运行，类似于 main 函数，run函数的调用靠的是 QThread 类中的start
private:
    bool stopped;
};

//MyThread2.c
#include "mythread2.h"
void MyThread2::run()
{
    static int i = 1;
    while(!stopped )
    {
        qDebug() <<"MyThread:"<<i;
        msleep(1000);  
        i+=2;
    }
    stopped = false;
}
void MyThread2::stop()
{
    stopped = true;
    
}

//  widget.h
MyThread thread;
MyThread2 thread2;
 //  widget.c

connect(ui->pushButton,&QPushButton::clicked(),this,[&](){  
	thread.start();
	ui->pushButton->setEnable(false);  // 按了一次启动线程之后，该按键不再起作用
	ui->pushButton_2->setEnable(true);
}); 

connect(ui->pushButton_2,&QPushButton::clicked(),this,[&](){  
if(thread.isRunning())
{
    thread.stop();
    ui->pushButton->setEnable(true);
	ui->pushButton_2->setEnable(false);
}
}); 

connect(ui->pushButton_3,&QPushButton::clicked(),this,[&](){  
	thread2.start();
	ui->pushButton_3->setEnable(false);  // 按了一次启动线程之后，该按键不再起作用
	ui->pushButton_4->setEnable(true);
}); 

connect(ui->pushButton_4,&QPushButton::clicked(),this,[&](){  
if(thread2.isRunning())
{
    thread.stop();
    ui->pushButton_3->setEnable(true);
	ui->pushButton_4->setEnable(false);
}
})
```



### section 11.4 线程间同步_信号量

两个线程抢夺资源，要实现线程之间的同步，如信号量，互斥锁，文件锁

Producer

```c++
//Producer.h

#include <QThread>
class Producer : public QThread
{
Q_OBJECT 
public:
	Producer(){stopped = false;}
    void stop();
protected:
	void run();  // 线程从 run 函数开始运行，类似于 main 函数，run函数的调用靠的是 QThread 类中的start
private:
    bool stopped;
};

//Producer.c
#include "Producer.h"
void Producer::run()
{
    int i =0;
    while(!stopped )
    {
        unusedSem.acquire(); // usedSem - 1
        buffer[i] = i;
        msleep(1000);
        qDebug()<<"Write to the shared memory.";
        usedSem.release();  // usedSem + 1
        ++i;
        i%=10;
    }
    stopped = false;
}
void Producer::stop()
{
    stopped = true;
    
}
```



Consumer

```c++
//Consumer.h

#include <QThread>
class Consumer : public QThread
{
Q_OBJECT 
public:
	Consumer(){stopped = false;}
    void stop();
protected:
	void run();  // 线程从 run 函数开始运行，类似于 main 函数，run函数的调用靠的是 QThread 类中的start
private:
    bool stopped;
};

//Consumer.c
#include "Consumer.h"
void Consumer::run()  // 立马用完 usedSem 
{
     int i = 0;
    while(!stopped )
    {
        usedSem.acquire();
        qDebug() <<buffer[i];
        unusedSem.release();
        ++i;
        i%=10;
    }
    stopped = false;
}
void Consumer::stop()
{
    stopped = true;
    
}
```



```c++

//  widget.h
Producer producer;
Consumer consumer;
 //  widget.c
 
 #include <QSemaphore>
 unsigned int buffer[10];
 QSemaphore usedSem(0);
 QSemaphore unusedSem(10);
 
 
 connect(ui->pushButton,&QPushButton::clicked(),this,[&](){  
	producer.start();
	ui->pushButton->setEnable(false);  // 按了一次启动线程之后，该按键不再起作用
	ui->pushButton_2->setEnable(true);
}); 

connect(ui->pushButton_2,&QPushButton::clicked(),this,[&](){  
if(thread.isRunning())
{
    producer.stop();
    ui->pushButton->setEnable(true);
	ui->pushButton_2->setEnable(false);
}
}); 

connect(ui->pushButton_3,&QPushButton::clicked(),this,[&](){  
	consumer.start();
	ui->pushButton_3->setEnable(false);  // 按了一次启动线程之后，该按键不再起作用
	ui->pushButton_4->setEnable(true);
}); 

connect(ui->pushButton_4,&QPushButton::clicked(),this,[&](){  
if(consumer.isRunning())
{
    consumer.stop();
    ui->pushButton_3->setEnable(true);
	ui->pushButton_4->setEnable(false);
}
})
```







## chapter 12 Qt 网络通信

网络编程分为 TCP，UDP协议，

TCP三次握手，TCP与UDP区别

### section 12.1 TCP 服务器

1. 服务器端：即主机

   套接字：一个用于监听 QTcpServer，一个用于通信 QTcpSocket

   监听 listen( ip, potr)   // ip 是服务器端的 ip

   收到信号，newConnection , 信号和槽

   产生新的套接字，clinet = server.nextPendingConnection()

   向客户端发送：client调用write

   收到数据：，信号和槽 readyRend()

2. 客户端

   只需要一个套接字 client

   1.连接服务器，connectToHost(ip, port)  // ip  port 是服务器端的 ip  port 

   2.发送  write()

   3.接收 readyRead()   ReadAll()

   4.断开连接 disConnectFromHost()

TCP  server 工程

服务器 ip地址是 127.0.0.1，主机环回地址

```c++
QT += core ui network
    
    
#include <QTcpServer>
#include <QTcpClient>
    
// 定义套接字
QTcpServer *tcpServer;  // 用于监听
QTcpSocket *clientConnection = nullptr;   // 用于通信

// 初始化
tcpServer = new QTcpServer(this);
clientConnection = new QTcpSocket(this);

// 监听网络
ui->lineEdit->setText("8080");
tcpServer->listen(QHostAddress::LocalHost,ui->lineEdit->text().toInt());

connect(tcpServer,&QTcpServer::newConnection,this,[&](){  // 监听到客户端有请求到来
    clientConnection = tcpServer->nextPendingConnection();  // 产生新的套接字，用于通信
    clientConnection->write("welcome to connect to server.");
    ui->textEdit->append("new connection......");
    
    // 客户端接收信号
    connect(clientConnection,&QTcpSocket::readyRead,this,[&](){
        QByteArray arr = clientConnection->readAll();
        ui->textEdit->append("server receive:" + arr);
        ui->textEdit->setAlignment(Qt::AlignRight);
    });
});

void Widget::on_pushButton_clicked()
{
    if( !clientConnection )  // 当 clientConnection 还是空指针
    {
        qDebug()<<"no connection";
        return;
    }
    else
    {
        clientConnection->write(ui->textEdit_2->toPlainText().toUtf8());  //toPlainText 就是读取textEdit_2 中的所有内容
        ui->textEdit->append("server send:" + ui->textEdit_2->toPlainText());
        ui->textEdit->setAlignment(Qt::AlignLeft);
        ui->textEdit_2->clear();  // 发送框清空
    }
    
}
```





### section 12.2 TCP 客户端



TCP client 工程

```c++
QT += core ui network
    
    
#include <QTcpServer>
#include <QTcpClient>
    
// 定义套接字
QTcpServer *tcpSocket; 


tcpSocket = new QTcpSocket(this);



void Widget::on_pushButton_clicked()
{
    tcpSocket->connectToHost(ui->lineEdit->text(),ui->lineEdit_2->text().toInt());
}


void Widget::on_pushButton_2_clicked()
{
    if( !tcpSocket )  
    {
        qDebug()<<"no connection";
        return;
    }
    else
    {
        tcpSocket->write(ui->textEdit_2->toPlainText().toUtf8());  
        ui->textEdit->append("client send: " + ui->textEdit_2->toPlainText());
        ui->textEdit_2->clear();  // 发送框清空
    }
}
```



### section 12.3 UDP 发送端接收端通信







### section 12.4 TCP 传输文件小案例

1. 客户端 client



```c++
QT += core ui network
    
    
#include <QTcpServer>
#include <QTcpSocket>
#incldue <QFileDialog>
  #include <QTimer>
    
// 定义套接字
QTcpSocket *tcpSocket; 
QString fileName;
Qint64 fileSize;
Qfile file;
QTimer myTimer;

tcpSocket = new QTcpSocket(this);
myTimer =new QTimer(this);


connect( tcpSocket,&QTcpSocket::readyRead,this,[&](){
    QByteArray arr =tcpSocket->readAll();
    qDebug()<<"client receive: "<<arr;
    });
connect(myTimer,&QTimer::timeout,this,[&](){  // 20ms 的定时器，定时结束再开始真正发送文件
    myTimer->stop();
    qint64 len;
    qint64 sendSize;
    do{
        len = 0;
        char buf[4*4096]; // 4kb
        len = file.read(buf,sizeof(buf));  // 实际读出来的值为 len
        tcpSocket->write(buf,len);
        sendSize += len;    // 累计已经发送文件大小
        ui->progressBar->setValue(sendSize/1024);  // 为了让数字不那么大，/1024
        
    }while(len>0)
    if(sendSize == fileSize)
    {
        file.close();  // 关闭文件
        tcpSocket->disconnectFromHost();  // 断开服务器的连接
        tcpSocket->close();  // 关闭套接字
    }
});


void Widget::on_pushButton_clicked()  // 点击连接
{
    tcpSocket->connectToHost(ui->lineEdit->text(),ui->lineEdit_2->text().toInt());
}


void Widget::on_pushButton_2_clicked()
{
    QString filePath = QFileDialog::getOpenFileName(this);  // 打开要发送的文件
    
    QFileInfo fileData(filePath);  
    fileName = fileData.fileName();  // 记录打开的文件名
    fileSize = fileData.size();      // 记录打开的文件大小
    qDebug()<<"name:"<<fileName<<", size:"<<fileSize;
    if(!filePath.isEmpty)
    {
        ui->lable_3->setText(filePath);
        file.setFileName(filePath);
        file.open(QIODevice::ReadOnly);
        
    }
    ui->progressBar->setMaximun(0);
    ui->progressBar->SetMinum(fileSize/1024);
    ui->progressBar->setValue(0);
}

void Widget::on_pushButton_3_clicked()  // 按键3 发送
{
    // 第一次发送信息，即文件的头信息 "文件名+###"+文件大小
    QString head = fileName+"###"+QString::number(fileSize); 
    qint64 length = tcpSocket->write(head.toUtf8());
    if(length>0)
    {
        myTimer->start(20);  // 20ms 的定时器，定时结束再开始真正发送文件
        qDebug()<<"send head successfully.";
    }
    else
    {
        qDebug()<<"failed to send head.";
        file.close();
    }
        
}
```





2. 服务器端 server





```c++
// 定义套接字
QTcpServer *tcpServer;  // 用于监听
QTcpSocket *clientConnection = nullptr;   // 用于通信
bool headInfo= true;
QString fileName;
qint64 fileSize;
qint64 recvSize;
QFile file;

// 初始化
tcpServer = new QTcpServer(this);
clientConnection = new QTcpSocket(this);


void Widget::on_pushButton_clicked()  // 点击连接
{
    // 监听网络

	tcpServer->listen(QHostAddress::LocalHost,8080);

	connect(tcpServer,&QTcpServer::newConnection,this,[&](){  // 监听到客户端有请求到来
    clientConnection = tcpServer->nextPendingConnection();  // 产生新的套接字，用于通信
    clientConnection->write("welcome to connect to server.");
    ui->textEdit->append("new connection......");
    
    // 客户端接收信号
    connect(clientConnection,&QTcpSocket::readyRead,this,[&](){
        QByteArray arr = clientConnection->readAll();
        
		if(headInfo)  // 第一次接收到信息，即文件的头信息 "文件名+###"+文件大小
        {
            headInfo = false;
            recvSize = 0;
            fileName = QString(arr).section("###",0,0);  
            fileSize = QString(arr).section("###",1,1).toInt();
            file.setFileName(filePath);
        	file.open(QIODevice::WriteOnly);
            
            
            ui->progressBar->setMaximun(0);
   			ui->progressBar->SetMinum(fileSize/1024);
   			ui->progressBar->setValue(0);
   		    
        }
        else
        {
            qint64 length = file.write(arr);
            if(length > 0)
            {
                recvSize += length;
            }
            ui->progressBar->setValue(recvSize/1024);  // 为了让数字不那么大，/1024
            if(recvSize == fileSize)
            {
                QMessageBox::information(this,“OK”,"reveived completed.");
                file.close();
            }
        }
        
    });
        ui->pushButton->setEnable(false);  // 第一次监听收到信号后，就不能再点击
}
```



## chapter13 使用 QtCharts

需要加装QtCharts安装包

安装步骤：



qmake



QtCharts：用于窗体中绘制曲线



```c++
QT += charts
```

编译通过，则说明导入QtCharts成功



---

## Qt 对象树



```c++
myButton = new QPushButton("CLOSE",this);  // == myButton->setParent(this); 本质都是调用 setParent()
// 设置父对象，因为窗口需要互相联系
// A需要显示在B上面，A需要指定B为父对象
```

对象树机制：父对象析构的时候，会先析构所有的子对象。目的就是方便管理内存，不用写 delete，系统会自动回收。

### styleSheet样式表

stylesheet 样式表的使用，允许自定义小部件的外观，Qt样式表的概念、术语和语法HTML层叠样式表（css）类似。

- 方法一：手动添加

```
label1->setStyleSheet(QStringLiteral(
                              "border-image: url(:/images/cil-user.svg);"   // 图片填充
                              "color:red;"  // 字体颜色
                              ));
 this->setStyleSheet(" QWidget{ background-color:blue;
 								color:green;} "
 );
```

- 方法二：使用 qss 文件

qss文件时使用Qt程序相关联的样式表文件，由GUI元素的外观和感觉，包括布局、颜色、鼠标的行为、大小和字体。它的风格，一个可以合并到一个UI。与HTML的CSS文件类似，是纯文本的格式定义，在应用程序运行时可以载入和解析这些样式定义，从而使应用程序的界面呈现不同效果







// 在资源文件中添加 .qss 文件

```c++
QWidget{ background-color:blue}
QLabel{ background-color:red;color:green }
```

// 读取文件

```c++
QFile file(":/style.qss");
/* 判断文件是否存在 */
if (file.exists() ) 
{
  /* 以只读的方式打开 */
  file.open(QFile::ReadOnly);
  /* 以字符串的方式保存读出的结果 */
  QString styleSheet = QLatin1String(file.readAll());
  /* 设置全局样式  qAPP是一个QObject的一个变量 */
  qApp->setStyleSheet(styleSheet);
  /* 关闭文件 */
  file.close();
}
```

这样样式表会应用于全局，要每一个单独设置

.qss 文件

```c++
QWidget{ background-color:blue}
QLabel{ background-color:red;color:green }
QLabel#label1 { background-color:blue  }  // 对 label 单独进行设置
```

代码中

```c++
myLabel->setObjectName("label1");
```

---

### Qt 控件

QWidget类是所有用户界面对象的基类，常用于顶层小部件或者子小部件

#### QPushButton 

QPushButton常用的四个信号：最常用 clicked(bool checked = false), pressed(), released(), 

toggled(bool checked)：开关

```c++
myButton2->setCheckable(true); // 设置为可选中，类似于开关
connect(myButton2,&QPushButton::toggled,this,[&](bool checked){qDebug()<<"button2 toggled"<<checked;});  // checked 是当前开关的状态，按一次为true，再按一次为false
```



QPushButton 案例：实现音乐播放的三个按键

```c++
formSong = new QPushButton("former",this);
    nextSong = new QPushButton("next",this);
    pauseOn = new QPushButton("on",this);

    pauseOn->setCheckable(true);
    formSong->setObjectName("formSong");
    nextSong->setObjectName("nextSong");
    pauseOn->setObjectName("pauseOn");
    formSong->setGeometry(200,0,80,60);
    pauseOn->setGeometry(300,0,80,60);
    nextSong->setGeometry(400,0,80,60);
    connect(pauseOn,&QPushButton::clicked,this,[&](bool checked){
        if(checked)
        {
            pauseOn->setText("paused");
        }
        else
        {
            pauseOn->setText("on");
        }
    });
formSong->show();
nextSong->show();
pauseOn->show();
```

qss文件

```c++
QPushButton#formSong {
    border-image:url(:/images/caret-left.svg);
    color: white; /* 文字颜色 */
    border-style: solid; /* 边框样式 */
    border-width: 2px; /* 边框宽度 */
    border-radius: 10px; /* 边框圆角 */
    border-color: #1c7cd6; /* 边框颜色 */
    padding: 5px; /* 内边距 */
}
QPushButton#pauseOn {
    border-image:url(:/images/pause-circle.svg);
    color: white; /* 文字颜色 */
    border-style: solid; /* 边框样式 */
    border-width: 2px; /* 边框宽度 */
    border-radius: 10px; /* 边框圆角 */
    border-color: #1c7cd6; /* 边框颜色 */
    padding: 5px; /* 内边距 */
}
QPushButton#pauseOn:checked {
    border-image:url(:/images/skip-start-circle.svg);
    color: white; /* 文字颜色 */
    border-style: solid; /* 边框样式 */
    border-width: 2px; /* 边框宽度 */
    border-radius: 10px; /* 边框圆角 */
    border-color: #1c7cd6; /* 边框颜色 */
    padding: 5px; /* 内边距 */
}
QPushButton#pauseOn:hover {
    background-color:green;
}
QPushButton#pauseOn:checked:hover {
    background-color:blue;
}

QPushButton#nextSong {
    border-image:url(:/images/caret-right.svg);
    color: white; /* 文字颜色 */

    border-style: solid; /* 边框样式 */
    border-width: 2px; /* 边框宽度 */
    border-radius: 10px; /* 边框圆角 */
    border-color: #1c7cd6; /* 边框颜色 */
    padding: 5px; /* 内边距 */
}
```

---

#### QRadioButton

 单选按钮，一般选择 toggled 信号

```c++
radioButton1 = new QRadioButton(this);
    radioButton2 = new QRadioButton(this);
    radioButton1->setGeometry(200,200,60,40);
    radioButton1->setText("one");
    radioButton2->setGeometry(300,200,60,40);
    radioButton2->setText("two");
    radioButton1->setChecked(false);
    radioButton2->setChecked(false);
```



qss文件

```c++
QRadioButton{
    font-size:20px;
}
QRadioButton:indicator:unchecked{
    border-image:url(:/images/icon/radioButton_unchecked.png);
    color:black;
}
QRadioButton:unchecked{
    color:black;
}
QRadioButton:indicator:checked{
    border-image:url(:/images/icon/radioButton_checked.png);
    color:green;
}
QRadioButton:checked{
    color:green;
}

```



#### QCheckBox 

复选按钮，三态按钮（tristate），一般只用两种状态（默认）



qss文件

```c++
QCheckBox::indictor:unchecked{
	image:url();

}
QCheckBox::indictor:indeterminate{
	image:url();

}
QCheckBox::indictor:checked{
	image:url();

}
```

信号多了 stateChanged(int arg1)

```c++
switch (arg1)
{
	case Qt::Checked:
		break;
	case Qt::PartiallyChecked:
		break;
	case Qt::Unchecked:
		break;
}
```



### 布局

#### margin&padding 外边距和内边距

margin：一个控件的边框到另一个控件的边框的距离，属于容器外部距离

padding：自身边框到自身内部另一个容器边框之间的距离，输入容器内距离

```
QPushButton{
	margin-top:50px;
	margin-button:50px;
}
```

#### 水平布局 QHBoxLayout 

HorizontalLayout 是不可视的，但是也是一个控件

多个可视部件，使用水平布局

spacing：每一个部件的间隔距离

stretch：拉伸因子（系数），控制水平方向的宽度（如1，2，3，即三个部件依次大小比例为1：2：3）

sizePolicy：大小策略，单个部件的属性（如Minumum，就是有一个最小大小，即缺省值，拉伸操作不能小于缺省值）

> 查看一个类的从属关系：选中后ctrl shift t
>



#### 垂直布局 QVBoxLayout 



```c++
this->setLayout(ui->horizontalLayout_1);  // 将widget 设置成和 ui 一样是水平布局，horizontalLayout_1是ui中的最外层布局名称
```



#### 网络布局 QGridLayout

```c++
this->setLayout(ui->gridLayout_1);  // 将 widget 设置成和 ui 一样是水平布局
```

三种布局用法类似



#### QSplitter 分裂器

**两个部件之间的左右拉伸分割线，是显示部件，也是布局，需要和布局一起使用。在电脑软件中常用**

handleWidth  分割线的宽度

background-color 分割线的背景颜色

orientation 设置方向

opaqueResize 为false时，在拖动时候会显示一条灰色的线条，在拖动到位并释放鼠标后再显示分割线条

​						默认为true，会实时更新子控件的大小

childrenCollapsible 为true时，用户可以将子部件的大小设置为 0

#### QSpacerItem 隔离弹簧

**需要和布局一起使用，有两种，Horizontal Spacer，Vertical Spacer**

orientation 设置方向，水平或垂直

sizeTypr 大小类型，可以设置固定大小，可扩大等

sizeHint 缺省大小，也即默认大小

```c++
// 需要和布局一起使用
this->setLayout(ui->horizontalLayout); 
```



### QLineEdit 单行输入框







仿QQ登录界面制作

#### QDialog 对话框

```c++
dialog->setModal(false);  // 设置模态，默认为false，浮动窗口

setWindowFlags(Qt::FramelessWindowHint);  // 无边框，最小化，悬浮和关闭这个框

setAttribute(Qt::WA_TranslucentBackground);  // 透明背景，将 QDialog设置成透明，就只剩下QFrame了，而QFrame可以设置border-radius
```



#### 	QScrollArea 面板滚动视图

页面过长/过宽时，右侧边缘和下方的滚动滑块

```c++
rgba(200,200,200,50%);  // 灰色，不透明度50%
```

hover，鼠标悬停时，设置样式



#### QTabWidget 面板切换效果

选项卡切换

与 QStackedWidget 类似，QStackedWidget 



#### QListWidget 列表控件



```c++
// 添加项
ui->listWidget->addItem("one");
ui->listWidget->addItem("two");

// 移除项
ui->listWidget->takeItem(0); // "one" 被移除

// 插入项
ui->listWidget->insertItem(0,"one");  // 从第 0 项前面插入



```

focusPolicy 设置为 NoFocus





#### QFile 文本读写



文本读写流程：

1. 打开文件，找到文件 QFileDialog：：getOpenFileName

   QFileDialog：：getOpenFileName 返回的是字符串，包括路径和文件名

2. open() 打开文件

3. 读写文件，readAll()，readLine()，write()

4. 关闭文件，close()，会自动保存

Qt中默认编码 utf-8，打开其他编码格式的文件会乱码

文件与网络都是通过 QIODevice 来读写

```c++
QString fileName = QFileDialog::getOpenFileName(this,"select file","/home/zhihongzeng/zzh/qtFile");
    qDebug()<<"file name="<<fileName;
    MyFile->setFileName(fileName);
    if( !MyFile->open(QIODevice::ReadWrite) )
    {
        qDebug() << "open failed.";
        return;
    }
    QByteArray arr = MyFile->readAll();
    ui->textEdit->setText(arr);
    MyFile->close();
```

```c++
void Lesson43QFile::on_pushButton_2_clicked()
{
    if(MyFile->fileName().isEmpty())
    {
        qDebug() << "close failed, because is empty.";
        return;
    }
    if( !MyFile->open(QIODevice::ReadWrite) )
    {
        qDebug() << "open failed.";
        return;
    }
    MyFile->write(ui->textEdit->toPlainText().toUtf8());
    MyFile->close();
    ui->textEdit->clear();
}
```

#### 动态创建文件

无论这个文件是否存在都会创建，有内容则保存，无内容则不保存，类似 ios 的备忘录







```c++
void Lesson44::on_pushButton_clicked()
{
    NewFileDialog *FileDialog = new NewFileDialog(this);
    FileDialog->setModal(true);
    FileDialog->resize(this->size());
    this->hide();
    FileDialog->show();
    FileDialog->exec();  // 不可省略
    
    QFile MyFile;
    MyFile.setFileName(QDateTime::currentDateTime().toString("MMddhhmmss"));
    // 以当前时间作为文件的命名，MMddhhmmss 就是月日时分秒
    MyFile.open(QIODevice::ReadWrite);  // 没有文件的话会创建文件
    MyFile.write(FileDialog->getTextEditContent().toUtf8());  // 在 FileDialog类中定义一个 public 成员函数，用来读出 textEdit 中内容
    if( 0 == FileDialog->getTextEditContent().length())  // 没有在文件中输入内容，则删除文件
        MyFile.remove();
    MyFile.close();
    delete FileDialog;
    this->show();
    
}
```



需要设置当前的路径

```c++
#include <QDir>
QApplication a(argc, argv);  // 在 application 创建后设置路径
QDir::setCurrent(QApplication::applicationDirPath());
```





### 内置库

#### QPainter 



```c++
void Lesson45QPainter::paintEvent(QPaintEvent *event)
{
    Q_UNUSED(event)
    QPainter painter(this);
    painter.setRenderHint(QPainter::Antialiasing);  // 设置为抗锯齿
    
    QPen pen;
    pen.setWidth(5);
    pen.setColor("red");
    painter.setPen(pen);  // 设置画笔
    
    QBrush brush(QColor("#986902"));
    painter.setBrush(brush); // 设置刷子，用来填充颜色
    
    painter.drawRect(10,10,100,100); // 矩形 （10，10）（100，100）

    QPolygon polygon;
    polygon.setPoints(3,110,20,220,100,320,100);
    painter.drawPolygon(polygon);  // 三角形 （110，20）（220，100）（320，100）


    painter.drawLine(100,120,300,120);  // 直线 （100，120）-> (300,100)

    painter.drawEllipse(200,200,80,40); // 椭圆原点（200，200），长轴80，短轴40
    
    QRectF rectF(0,0,200,100);  // 指定画文字的区域
    painter.drawText(rectF,Qt::AlignHCenter,"OK");
    
    QPainterPath path;
    path.moveTo(20,80);  // 起始点
    path.lineTo(200,200);  // 直线
    path.cubicTo(100,200,200,300,400,500);  // 贝塞尔曲线
    painter.drawPath(path);

}
```

应用案例：轮播文字







#### QChart 图表

ui 中没有 QChart，但是有 Graphics View ，可以将其提升为 QChartView

两种方法添加 QChart：

1. ui中加入 Graphics View，将其提升为QChartView
2. 在代码中定义 QChartView



```c++
#include <QChart>
#include <QChartView>  // 图表的显示
#include <QSplineSeries> // 曲线
#include <QLineSeries>  // 折线

QChartView *ChartView = new QChartView(this);
QChart *chart = new QChart();

QValueAxis *valueAxisX = new QValueAxis();
QValueAxis *valueAxisY = new QValueAxis();

valueAxisX->setRange(0,5000);
valueAxisY->setRange(0,100);

valueAxisX->setTitle("time/ms");
valueAxisY->setTitle("temp/°C");

valueAxisX->setLabelFormat("%d");
valueAxisY->setLabelFormat("%d");

// 给图标设置坐标轴
chart->setDefaultAxes();
chart->addAxis(valueAxisX,Qt::AlignBottom)
chart->addAxis(valueAxisY,Qt::AlignLeft)

// 图标标题
chart->setTitle("temperature curve");

// 设置图例
chart->legend().setVisible(false);

// 曲线
QSplineSeries SlineSeries = new QSplineSeries();
SlineSeries->append(0,50);
SlineSeries->append(1000,65);
SlineSeries->append(2000,69);
SlineSeries->append(3000,80);
SlineSeries->append(4000,88);
SlineSeries->append(1000,90);

QPen pen(QColor(0xff5657));
SlineSeries->setPen(pen);

// 图表中添加曲线
chart->addSeries(SlineSeries);

// 将曲线中的数据与坐标轴相连
SlineSeries->attachAxis(valueAxisX);
SlineSeries->attachAxis(valueAxisY);

// 将图表放置在图表视图中
ChartView->setChart(chart);

```



#### QPropertyAnimation 属性动画



1. 几何动画



2. 颜色动画



3. 不透明动画



#### QPROPERTY 宏



```c++
QPROPERTY(qreal money READ money)

qreal money() const;
```



案例，使用 QPROPERTY 宏 来实现自定义属性动画



#### QThread 线程

```c++
system("sleep 5");  // 相当于延时 5s
```

一个应用程序至少一个进程，一个进程至少会有一个线程

QProcess 创建进程

QThread 创建线程

```c++
class MyThread : public QThread
{
    Q_OBJECT
    public:
        explicit MyThread(QWidget *parent = 0)
        {
            Q_UNUSED(parent)
        }
        ~MyThread()
        {}
    void run() override{
        qDebug() << "thread start.";
        sleep(3);
        qDebug() << "thread end.";
        deleteLater();  // 线程运行完，销毁线程开辟空间
    }

};

/* 创建线程 */
MyThread *TestThread = new MyThread(this);
/* 开启线程 */
TestThread->start();
/* 终止线程 */
if(!myThread->isFinished())
  myThread->terminate();

```



```
ps -aux | grep Lesson51QThread
ps -mp 4693
ls/proc/4693/task
```





### TCP

#### QServer





```c++
#include "lesson52server.h"
#include "ui_lesson52server.h"

Lesson52Server::Lesson52Server(QWidget *parent) :
    QWidget(parent),
    ui(new Ui::Lesson52Server)
{
    ui->setupUi(this);
    myTcpServer = new QTcpServer(this);
    connect(myTcpServer,SIGNAL(newConnection()),this,SLOT(mNewConnection()));

}

Lesson52Server::~Lesson52Server()
{
    delete ui;
}

void Lesson52Server::mNewConnection()
{
    //
    QTcpSocket *tempTcpSocket = myTcpServer->nextPendingConnection();

    //
    ui->textBrowser->append("client ip: "+ tempTcpSocket->peerAddress().toString());
//    ui->textBrowser->append("client ip: "+ tempTcpSocket->peerPort().toString());
    ui->textBrowser->append("client ip: "+ QString::number(tempTcpSocket->peerPort()));


    //
    connect(tempTcpSocket,SIGNAL(readyRead()),SLOT(mReceiveMessage()));
    connect(tempTcpSocket,SIGNAL(stateChanged(QAbstractSocket::SocketState)),this,SLOT(mStateChange(QAbstractSocket::SocketState)));
}

void Lesson52Server::mReceiveMessage()
{
    QTcpSocket *tempSocket = (QTcpSocket *)sender();
    ui->textBrowser->append(tempSocket->readAll());
}

void Lesson52Server::mStateChange(QAbstractSocket::SocketState state)
{
    //
    QTcpSocket *tempSocket = (QTcpSocket *)sender();
    qDebug()<<"state changed,"<<state;
    switch (state) {
    case QAbstractSocket::UnconnectedState :
        qDebug()<<"disconnected";
        ui->textBrowser->append("client disconnected");
        tempSocket->deleteLater(); //
        break;
    case QAbstractSocket::ConnectedState :
        qDebug()<<"connected";
        ui->textBrowser->append("client is connected");
        break;
    default:
        break;
    }

}

void Lesson52Server::on_pushButton_3_clicked()
{
    QList <QTcpSocket *> SocketList = myTcpServer->findChildren<QTcpSocket *>();
    qDebug()<<"tcpsocket number: "<<SocketList.count();
    foreach (QTcpSocket *temp, SocketList)
    {
        temp->write(ui->lineEdit->text().toUtf8());
    }
}

void Lesson52Server::on_pushButton_clicked()
{
    myTcpServer->listen(QHostAddress("127.0.0.1"),9999);
}
void Lesson52Server::on_pushButton_2_clicked()
{
    myTcpServer->close();
}

```











#### UDP 通信

UDP收发器器1与UDP收发器2相互通信

UDP消息传送分为：单播、广播、组播 三种模式

1. 单播unicast：用于两个主机之间的端对端通信，需要知道对方的IP地址和端口
2. 广播broadcast：区别是IP地址不同，广播一般使用广播地址255.255.255.255，将消息发送到同一广播（也可以是局域网内同一网段）网络上的每个主机
3. 组播multicast：多点广播



## STM32开发板Qt开发

### 交叉编译Qt项目

#### 安装交叉编译器

在 ubuntu 中安装 .sh 脚本文件

```linux
chmod 777 ./*.sh
sudo ./*.sh
```

默认安装在 /opt

每次使用安装后的该脚本，都需要source添加环境变量

```linux
source
```

source是一个内置的Shell命令，它读取并执行当前Shell中文件的内容，仅对当前 terminal 有效

source 之后 qmake-v 才有效

```
source /opt/fsl-imx-x11/4.1.15-2.1.0/environment-setup-cortexa7hf-neon-poky-linux-gnueabi
```



#### 命令行交叉编译Qt项目

> qmake -v  

会打印 qmake 版本信息

在 Lesson55QUdp.pro 文件夹下

> qmake Lesson55QUdp.pro

执行完会生成 Makefile 文件，以及一些隐藏文件（ls -a查看，ls -l显示全部文件的详细信息）

> make distclean  // 不用

会删除上述 qmake 生成的所有文件

> make -j 2    // 使用2两个线程进行编译，不能大于内核数量，实际是调用 gcc

根据makefile进行编译，执行完会生成  Lesson55QUdp 的可执行文件（ls -l可看到带x属性）

> make clean  // 需要用

只会删除一部分多余的么用文件







#### 拷贝编译好的执行程序到开发板上

---

方法一：U盘

通过U盘，U盘在ubuntu目录 /media/zhihongzeng/ 下面，cp 拷贝，拷贝完 sync 同步数据

通过U盘拷贝可执行文件到开发板

需要先关闭开发板当前运行的程序，killall+程序名

然后修改默认启动的程序

---

方法二：通过网络

开发板与电脑处于同一网络



```linux
scp 文件 用户名@ip地址：路径
scp -r 文件夹 用户名@ip地址：路径

scp Lesson55QUdp root@开发板ip :/home/root
// 将 Lesson55QUdp 拷贝到 开发板的 /home/root 目录下，开发板的默认用户名为 root
```



#### STM32 交叉编译Qt项目







#### Qt点亮开发板的LED

Qt → 驱动提供接口 → 开发板硬件

---

1. 开发板的 Linux 中找到 LED

内核设备树路径 arch/arm/boot/dts/stm32mp157d-atk.dtsi

里面注册有硬件中的 LED，LED注册成了 gpio-led 类型设备，通过应用层接口操作 LED

```c++
#include "lesson58led.h"
#include "ui_lesson58led.h"
#include <QDebug>

Lesson58Led::Lesson58Led(QWidget *parent) :
    QWidget(parent),
    ui(new Ui::Lesson58Led)
{
    ui->setupUi(this);

    MyFile.setFileName("/sys/class/leds/sys-led/brightness");
    // 设置触发模式为 none
    system("echo none > /sys/class/leds/sys-led/trigger");
}

void Lesson58Led::on_pushButton_clicked(bool checked)
{
    if( !MyFile.exists() )
    {
        qDebug() <<" no led.";
        return;
    }
    else
    {
        if( !MyFile.open(QIODevice::WriteOnly) )
            return;
        QByteArray buff[] = {"0","1"};
        if(checked)
        {
            ui->pushButton->setText("off");
            MyFile.write(buff[1]);

        }
        else
        {
            ui->pushButton->setText("on");
            MyFile.write(buff[0]);
        }

        MyFile.close();
    }
}
```

#### 使用开发板的按键



Linux 系统中集成了键盘，因此可以设置按键为键盘中的某个键

Qt::Key_VolumeDown 的按键编号为 114

```c++
void Lesson60Btn::keyPressEvent(QKeyEvent *event)
{
    if( event->key() == Qt::Key_1 )
    {
        this->setStyleSheet("QWidget{background-color:red;}");
    }
    qDebug()<<"key pressed.";
}

void Lesson60Btn::keyReleaseEvent(QKeyEvent *event)
{
    if( event->key() == Qt::Key_1 )
    {
        this->setStyleSheet("QWidget{background-color:white;}");
    }
}
```

#### 串口上位机



要在ubuntu中连接串口，需要 chmod 777 /dev/ttyUSB





### 嵌入式Qt嵌入式移植概述

---

为什么要移植：

自制系统，如BusyBox简易系统，希望能运行Qt

厂家提供的Qt库太大，太全，占空间

版本升级，想用其他好用的Qt版本

---

方法：

1. 编译Qt源码（在Qt官网的single中），得到Qt库文件（Linux中.so后缀，Windows中 .dll 后缀），部署到嵌入式系统中
2. **利用开源的嵌入式 Linux 系统自动构建框架 Buildroot** 或者 Yocto

**Buildroot ：很方便**

Yocto：容易编译出错，而且文件巨大，不学

三种方法对比

|                |               单独编译Qt源码（移植到BusyBox）                |        Buildroot构建Qt         |          Yocto构建Qt           |
| -------------- | :----------------------------------------------------------: | :----------------------------: | :----------------------------: |
| **难度**       |                     相当大，依赖第三方库                     |     小，需要什么就勾选就行     |               大               |
| **Qt库完整性** |                            不完整                            |              完整              |              完整              |
| **复杂程度**   |                             复杂                             |              一般              |              一般              |
| **缺少的功能** | 播放媒体（Qt是调用gstreamer媒体去解码），网络功能（需要python）等 |    较完整，需要配置依赖编译    |    较完整，需要配置依赖编译    |
| **适用场合**   |                     运行简单的Qt图形界面                     | 功能完善，媒体，网络等功能可用 | 功能完善，媒体，网络等功能可用 |







## git 使用

下载

```
git clone http
```



```
git branch -a // 查看 branch
git tag -l  // 查看 tag
```





```
git checkout -b 9.99 1.21  // 切换到 9.99（随意命名） branch 的 1.21 tag
```

linux中terminal粘贴为鼠标中键

解压缩

```
tar xf ./*.tar.xz
tar xf ./*.tar.xz -c /home  // 解压到 /home 目录下 
```





利用面向对象学开发的一套gui组件库，gui实现细节封装于类的内部，具有跨平台的特性

跨平台程序开发库

Qt：使用面向对象的方法，将操作系统提供的原生的gui相关的函数全部封装成类。是一套跨平台的gui开发库，C++集成开发环境

gui graphic user interface















```c++
QWidget *w = new QWidget(NULL,Qt::WindowCloseButtonHint);
```

Qt::WindowStaysOnTopHint

代码实现：按照设计编程实现，重心在于功能实现

软件开发过程中的重构：refactoring

以改善代码质量为目的的软件架构优化

使其软件的设计和架构更加合理

提高软件的扩展性和维护性

重构是维持代码质量在可接受范围内的重要方式，







二阶构造法

```c++

class QCalcultorUI
{
private
	QCalcultorUI():
   bool constract();
public:
	static QCalcultorUI* NewInstance();
}


bool QCalcultorUI::construct()
{
    bool ret = true;
    // 
    QPushButton btn = new QPushButton(this);
    if(btn != NULL)
    {
        
    }
    else
        ret = false;
    return ret;
}
static QCalcultorUI* QCalcultorUI::NewInstance()
{
	QCalcultorUI* ret = new QCalcultorUI();
    if( (ret == NULL) || ret->construct())
    {
        delete ret;
        ret = NULL;
    }
	return ret;
}

// 要使用该class
int main()
{
    QApplication a();
    QCalculator* cal = QCalculator::NewInstance(); 
    itn ret = -1;
    if(cal != NULL)
    {
        cal->show();
        ret = a.exec();
        delete cal;
    }
    return ret;
} 
```



Qt封装了具体操作系统的消息机制，遵循经典的GUI消息驱动事件模型

所有自定义槽的类必须在声明的开始处加上 Q_OBJECT(宏)，且QObject的子类才能定义槽





qt中的槽函数中识别发送者

```c++
void onBtnClicked()
{
    QPushButton* btn = (QPushButton *) sender();
}

```

#### Qt中的字符串类

C中不支持真正意义的字符串，用字符数组和一组函数来实现字符串操作，C不支持自定义类型，因此无法获得字符串类型

C到C++进化过程中，引入了自定义类型。C++原生类型系统不包含字符串类型

STL是一套需要与C++一同发布的标准库，是一套以模板技术实现完成的C++类库，包含了常用的算法和数据结构，包含了字符串类

STL中有字符串类，为什么Qt中需要Qt的字符串类

> STL的具体实现依赖于编译器生产厂商，STL的标准只是其接口是标准的（相同的全局函数，相同的算法类和数据结构类，相同的类成员函数），不同厂商的编译器所带的STL存在差异，依赖于STL开发的C++程序在不同平台的行为会出现差异

Qt中的QString比STL中的sting更强大易用。因此，需要跨平台的时候，应使用Qt中的在字符串库

Qt的字符串类特点

- 采用**Unicode**编码（说明支持中日韩文，STL为assic编码，要使用宽字符来支持中文）
- 使用**隐式共享技术（结合了深拷贝和浅拷贝的优点）**来节省内存和不必要的数据拷贝
- **跨平台**使用，不必考虑字符串的平台兼容性

QString直接支持字符串和数字的相互转换

直接支持字符串的大小比较

直接支持不同字符编码间的相互转换

直接支持std::string和std::wstring的相互转换

直接支持正则表达式的应用


toInt，toShort，toUInt等字符串转换为数

```
#include "widget.h"
#include <QApplication>
#include <QPushButton>
#include <QDebug>

void Sample_1()
{
    QString s = "add";
    s.append(" ");
    s.append("Qt");
    s.prepend(" ");
    s.prepend("C++");
    qDebug()<<s;
    s.replace("add","&&");
    qDebug()<<s;
}

void Sample_2()
{
    QString s = "";
    int index = 0;
    s.sprintf("%d. I'm %s, thank you!",1,"Delphi Tang");
    qDebug()<<s;
    index = s.indexOf(",");
    s = s.mid(0,index);
    qDebug()<<s;
    index  = s.indexOf(".");
    s= s.mid(index+1,s.length());  //" I'm Delphi Tang"
    s = s.trimmed();  // delete " "
    qDebug()<<s;
    index = s.indexOf(" ");
    s = s.mid(index+1,s.length());
    qDebug()<<s;
}
void Sample_3(QString *a,int len)  // 选择排序 
{
    for(int i =0;i<len;++i)
    {
        for(int j = i+1;j<len;++j)
        {
            if(a[j]<a[i])
            {
                QString temp = a[i];
                a[i] = a[j];
                a[j] = temp;
            }
        }
    }
}

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    qDebug()<<"sample 1:";
    Sample_1();
    qDebug()<<endl<<"sample 2:";
    Sample_2();
    qDebug()<<endl<<"sample 3:";

    QString company[6]={
        QString("Cracle"),
        QString("Borland"),
        QString("Microsoft"),
        QString("IBM"),
        QString("D.T.Software"),
        QString("AK"),
    };
    Sample_3(company,6);
    for(int i =0;i<6;++i)
    {
        qDebug() << company[i];
    }

    return a.exec();
}
```



计算器的核心算法：先分离数字和运算符，将中缀表达式转换为后缀表达式，通过后缀表达式计算最终结果

后缀表达式符合计算机的运算方式，消除了中缀表达式中的括号，同时保留了运算优先级

```
5+3 → 53+
1+2*3 → 123*+
9+（3-1）*5 → 931-5*+
```

1. 分离算法：中缀表达式中含有：    数字和小数点，  符号+-  ，  运算符+-*/  ，括号（）【】

```c++
定义累计变量num
当前字符exp[i]为数字或小数点时
	累计：num+=exp[i]
当前字符为符号时
	若num为运算数，分离并保存
	若exp[i]为正负号：
		累计符号位+和-：num+=exp[i]
	若exp[i]为运算符：
		分离比保存
难点：如何识别正负号和加减号
正负号：+和-在表达式的第一个位置
		括号后的+和-
		运算符后的+和-
因此，就看+-号的前一个字符是什么，就可以判断出是否为正负号。前一个字符如果是 +-*/ （
```



QChar是Qt中的字符类型，Unicode占两个字节，因此QChar是两个字符









Qt中的上标

| 上标 | Unicode |            |
| ---- | ------- | ---------- |
| 0    | 2070    | C++        |
| 1    | 00B9    | \xE2\81\80 |
| 2    | 00B2    | \xC2\xB9   |
| 3    | 00B3    | \xC2\xB2   |
| 4    | 2074    | \xC2\xB3   |
| 5    | 2075    | \xE2\81\B4 |
| 6    | 2076    | \xE2\81\B5 |
| 7    | 2077    | \xE2\81\B6 |
| 8    | 2078    | \xE2\81\B7 |
| 9    | 2079    | \xE2\81\B8 |

```c++
// 方法一
QString str = QString::fromUtf8("m\xC2\xB2");
// 方法二
quint16 square = 0xB2;
QString paintStr = "m" + QString::fromUtf16(&square, 1);
```







