# 传智Qt



Qt的介绍、优点、成功案例

Qt是一个跨平台的C++图形用户界面应用程序框架。它为应用程序开发者提供建立艺术级图形界面所需的所有功能。它是完全面向对象的，容易扩展，并且允许真正的组件编程。



---

优点：

- 跨平台，几乎支持所有的平台

- 接口简单，容易上手，学习Qt框架对学习其他框架有参考意义
- 一定程度上简化了内存回收机制
- 开发效率高，能够快速的构建应用程序
- 社区氛围很好，市场份额高
- 可以进行嵌入式开发

svn、vss、git 版本控制系统

```git
update   // 需要update到最新代码，然后写自己的
slove    // 之前没有update，最新版本别人修改了，你需要提交修改，就要 slove 冲突
commit   // 解决了冲突，提交
```

Analyze是对QML开发

```c++
#include <QApplication>   // 包含应用程序类的头文件 
argc:命令行变量的数量
argv:命令行变量的数组
QApplication a(argc,argv);  // 应用程序对象a，在Qt中，应用程序对象有且仅有一个
myWidget w;  // 实例化窗口对象
return a.exec();  //让应用程序对象a进入消息循环，让代码阻塞到这行

TEMPLATE = app  // 模板，应用程序模板 Application
```



类名：首字母大写，单词之间首字母大写

函数、变量名称：首字母小写，单词之间首字母大写

字体缩放：ctrl 滚轮

整行移动：ctrl  shift ⬆ ⬇

自动对齐：ctrl i

重名之间的.h和.c切换： F4

帮助文档：Tools/mingw49_32/bin/assistant.exe

熟悉按钮控件常用API

```c++
pushButton->move(100,100);
setFixedSize(800,480);  // 固定窗口大小，或 resize(); this->resize()
```

对象树：当创建的对象在堆区时候，如果指定的父类是QObject派生下来的类或者QObject子类派生下来的类，可以不用管理释放的操作，对象会放入到对象树种。

析构顺序与构造顺序相反

## 信号与槽

---

信号槽优点：松散耦合，信号发送方和接受方本身没有关联，通过connect连接，将两端耦合在一起

**一个信号可以连接多个槽函数，但是槽函数的执行顺序是随机的，不能控制**

信号：返回值为void，只需要声明，可以有参数，可以重载

槽：返回值void，需要声明和实现，可以有参数，可以重载，一般写在public slots下（高版本可以写在public下的函数或者全局函数）

```c++
// signal
void Teacher::hunger();
// slot
void Student::treat();

connect(techer,&Teacher::hunger,studnt,&Student::treat);

// signal
void Teacher::hunger(QString);
// slot
void Student::treat(QString);
// 使用重载的信号和槽
void (Teacher:: *teacherSignal)(QString) = &Teacher::hungry;   // 定义函数指针
void (Student:: *studentSlot)(QString) = &Student::treat;
connect(techer,teacherSignal,studnt,studentSlot);
```

信号和槽函数的参数

- **类型必须一一对应**

- **信号的参数个数可以比槽函数要多，但是前面的参数类型要匹配**

  signal(QString, int);   slots(QString) ;   OK

  signal(int, int);           slots(QString) ;   ERROR，Qt5会报错，Qt4只是 warning

  signal(clicked());        signal( func(int) ) ;   ERROR

Qt4 版本写法

```c++
connect(techer,SIGNAL(hunger()),studnt,SLOT(treat()));  // 连接无参信号和槽
connect(techer,SIGNAL(hunger(QString)),studnt,SLOT(treat(QString))); // 连接有参信号和槽
connect(techer,SIGNAL(hunger()),studnt,SLOT(treat(QString)));  // 编译通过，缺点：类型不做检测
```

断开信号：

```c++
disconnect(techer,teacherSignal,studnt,studentSlot);
```



---

Qt4中.pro中添加 CONFIG += c++11

Lambda表达式用于定义并创建匿名的函数对象

```c++
[](){}
[=]  可以使用Lambda所在范围内所有可见的局部变量，包括this，并且是值传递方式。默认情况下，变量都是只读的，除非加上 multable
[&]  可以使用Lambda所在范围内所有可见的局部变量，包括this，并且是引用传递方式
[this]  可以使用Lambda所在类中的成员变量，相当于 =
[a]   将a按值传递
[&a]  将a按引用传递
[a, &b]   将a按值传递，b按引用传递
[=, &a, &b]   除了a、b按引用传递外，其他参数都按值传递
[&, a, b]   除了a、b按值传递外，其他参数都按引用传递

[](){}();  // 直接定义并调用
[=]  // 最常用
```



---

**multable关键字，很少使用**

**按值传递函数对象参数时，加上multable修饰符后，可以修改按值传递进来的拷贝（只能修改拷贝，而不是值本体）**

```c++
int m =10;
connect(,,this,[m]()mutable { m=100+10; qDebug()<<m; } );  // 110，只是修改了m的拷贝
qDebug()<<m;  // 10
// [m]默认m是只读的，去掉 mutable 会报错
```



---

**Lambad表达式的返回值**

```c++
int ret = []()->int{return 1000;}();
```





```c++
QString foodName = "egg";
qDebug()<<foodName;  // "egg"
qDebug()<<foodName.toUtf8();  // "egg"
qDebug()<<foodName.toUtf8().data();  // egg   QString  .toUtf8()→ QByteArray  .data()→ char *
```



---

## 组件

### QMainWindow

一个为用于提供主窗口程序的类，包含一个菜单栏 **menu bar**，多个工具栏 **tool bars**，多个浮动窗口 **dock widgets**，一个状态栏 **status bar**，及一个中心部件 **central widget**，是许多应用程序的基础，如文本编辑器，图片编辑器等。

中心部件、菜单栏、状态栏只能有一个 set，其他可以有多个add

```c++
/*---------------------------------------------------*/
// 创建菜单栏
QMenuBar* bar = new QMenuBar();
// 将菜单栏放入窗口中
setMenuBar(bar);

// 创建菜单
QMenu* fileMenu = bar->addMenu("file");
QMenu* editMenu = bar->addMenu("edit");

// 创建菜单项
QAction* newAction = fileMenu->addAction("new");
fileMenu->addSeparator();  // 添加分隔线
QAction* openAction = fileMenu->addAction("open");

/*---------------------------------------------------*/
// 工具栏
QToolBar* toolBar = new QToolBar(this);
addToolBar(Qt::LeftToolBarArea,toolBar);  // 放置工具栏
toolBar->setAllowedAreas(Qt::LeftToolBarArea | Qt::RightToolBarArea); // 工具栏位置
toolBar->setFloatable(false);  // 禁止浮动
toolBar->setMovable(false);   // 禁止移动（总开关）
// 添加内容
toolBar->addAction(newAction);
toolBar->addSeparator();
toolBar->addAction(openAction);
QPushButton* btn = new QPushButton("aa",this);
toolBar->addWidget(btn);

/*---------------------------------------------------*/
// 状态栏
QStatusBar* stBar = new QStatusBar();
setStatusBar(stBar);  // 放置状态栏
QLabel* label = new QLabel("notice",this); //
stBar->AddWidget(label);
QLabel* label2 = new QLabel("右侧提示信息",this); //
stBar->addPermanentWindow(label2);

/*---------------------------------------------------*/
// 浮动窗口
QDockWidget* dockWidget = new QDockWidget("float",this);
addDockWidget(Qt::BottomDockWidgetArea,dockWidget);
dockWidget->setAllowedAreas(Qt::TopDockWidgetArea | Qt::BottomDockWidgetArea);

/*---------------------------------------------------*/
// 中心部件
QTextEdit* textEdit = new QTextEdit(this);
setCentralWidget(textEdit);


newAction->setIcon(QIcon(":/icons/icons/icon1.jpg"));
```

### QDialog 对话框

分为模态（不可操作其它窗口）和非模态对话框

```c++
// 模态，一般设置在栈上，关闭后就销毁
QDialog dlg = QDialog(this);
dlg->exec();  // 模态，阻塞在此

// 非模块
void slotShowDialog(){
QDialog* dlg = new QDialog(this);  // 防止一闪而过，创建在堆区
dlg->show();
dlg->setAttribute(Qt::WA_DeleteOnClose);  // 当关闭的时候就释放空间，防止无限制创建
}
```

标准对话框：

| QColorDialog    | 选择颜色                             |
| --------------- | ------------------------------------ |
| QFileDialog     | 选择文件或目录                       |
| QFontDiaglog    | 选择字体                             |
| QInputDialog    | 允许用户输入一个值，并将其值返回     |
| QMessageBox     | 模态对话框，用于显示信息，询问问题等 |
| QProgressDialog | 显示操作过程                         |

消息对话框

```c++
// 方法一：询问对话框，显示两个按键（Save、Cancel），默认选择Save
QMessageBox::question(this,"ques","a question",QMessageBox::Save|QMessageBox::Cancel,QMessageBox::Save);

// 方法二：捕获询问对话框的返回值
if( QMessageBox::Save == QMessageBox::question(this,"ques","a question",QMessageBox::Save|QMessageBox::Cancel,QMessageBox::Save) )
{
    qDebug()<<"save clicked";
} 
```

  颜色对话框捕获所选颜色

```c++
QColor color = QColorDialog::getColor( QColor(255,0,0) );  // QColor(255,0,0,255)  255是透明度，默认不透明
```

文件对话框

```c++
QString str = QFileDialog::getOpenFileName(this,"open file",":/","(*.txt)");
```

字体对话框

```c++
bool flag;
QFont font = QFontDialog::getFont(&flag,QFont("Ubuntu",36));
```



按键背景是图片+文字，使用toolbutton，修改 toolButtonStyle，凸起风格 autoRaise

radiobutton  单选按钮 与 group box搭配使用

```c++
// radio 设置默认
ui->BtnMan->setChecked(true);
```

checkbox 多选按钮，其属性 tristate 勾选可以设置三态（0未选中，1半选中，2选中）

listWidget

```c++
    QListWidgetItem* item = new QListWidgetItem("12345");
    item->setTextAlignment(Qt::AlignHCenter);
    ui->listWidget->addItem(item);

    QStringList list;
    list<<"12345"<<"okkk"<<"6666";
    ui->listWidget->addItems(list);  // == ui->listWidget->addItems( QStringList()<<"12345"<<"okkk"<<"6666" );
```

treeWidget

```c++
	 // 设置头
	 ui->treeWidget->setHeaderLabels(QStringList()<<"ID"<<"details");

    // 设置根节点
    QTreeWidgetItem* item11 = new QTreeWidgetItem(QStringList()<<"0x11");
    QTreeWidgetItem* item12 = new QTreeWidgetItem(QStringList()<<"0x12");
    QTreeWidgetItem* item13 = new QTreeWidgetItem(QStringList()<<"0x13");
    ui->treeWidget->addTopLevelItem(item11);
    ui->treeWidget->addTopLevelItem(item12);
    ui->treeWidget->addTopLevelItem(item13);

    // 追加子节点
    QTreeWidgetItem* subitem11 = new QTreeWidgetItem(QStringList()<<"one"<<"two");
    item11->addChild(subitem11);
```

tableWidget

```c++
	// 设置行列数
    ui->tableWidget->setColumnCount(3);
    ui->tableWidget->setRowCount(5);
    // 设置水平表头
    ui->tableWidget->setHorizontalHeaderLabels(QStringList()<<"name"<<"sex"<<"age");

    QStringList nameList;
    nameList<<"Tom"<<"LiHua"<<"Lily"<<"Micro"<<"Keely";
    QStringList sexList;
    sexList<<"male"<<"male"<<"female"<<"male"<<"female";
    QStringList ageList;
    ageList<<"10"<<"11"<<"12"<<"13"<<"14";
    for (int i = 0; i<5; ++i)
    {
        int col = 0;
        ui->tableWidget->setItem(i,col++,new QTableWidgetItem(nameList[i]));
        ui->tableWidget->setItem(i,col++,new QTableWidgetItem(sexList.at(i)));
        ui->tableWidget->setItem(i,col++,new QTableWidgetItem(ageList.at(i)));
    }
```

QStringList nameList; 等价于 QList<QString> nameList;

comboBox 下拉选择框

label 显示图片和动图（gif）

```C++
ui->label->setPixmap( QPixmap(":/icons/icons/icon1.jpg") );

QMovie* movie = new QMovie(":/icons/icons/mario.gif");
ui->label2->setMovie(movie);
movie->start();  // 播放动图
```

### 自定义控件封装



步骤：

1.  创建一个设计师界面类 SmallWidget
2.  SmallWidget的.ui中设计 QSpingBox 和 QSlider 控件，.cpp中实现功能，提供对外接口
3.  MyWidget 中使用自定义控件，拖拽一个 Widget 控件，提升为 SmallWidget



#### 鼠标事件 QEnterEvent



重写 virtual 函数时，virtual 可加可不加



鼠标进入事件  enterEvent

鼠标离开事件  leaveEvent

鼠标按下事件  mousePressEvent(QMouseEvent ev)

鼠标释放事件  mouseReleaseEvent(QMouseEvent ev)

鼠标移动事件  mouseMoveEvent(QMouseEvent ev)

```c++
// 格式化字符串
QString str = QString( "x=%1, y =%2" ).arg(ev->x()).arg(ev->y());
qDebug()<<str;
```



**定时器 QTimerEvent**

---

**第一种方式，利用事件**

void timerEvent(QTimerEvent* ev)

```c++
id1 = startTimer(1000);  // .h 中定义 int id1;
id2 = startTimer(2000);  // 2s 定时
    
void Widget::timerEvent(QTimerEvent *ev)
{
    if(ev->timerId() == id1)
    {
    static int num = 1;
    ui->label_2->setText(QString::number(num++));
    }
    else if(ev->timerId() == id2)
    {
    static int num2 = 1;
    ui->label_3->setText(QString::number(num2++));
    }
}
```

---

**推荐使用第二种方式：利用类 QTimer**

```c++
#include <QTimer>
QTimer* timer = new QTimer(this);
timer->start(500);  // 500ms
// 关联定时器事件的槽函数
connect(timer,&QTimer::timeout,ui->label_4,[=](){
   static int num = 1;
   ui->label_4->setText(QString::number(num++));
});
// 通过按键停止定时器
connect(ui->pushButton,&QPushButton::clicked,this,[=](){
        timer->stop();
    });
```



---

事件分发器

bool event(QEvent* ev)

返回为true：代表用户要自己处理这个事件，不向下分发

用于事件的分发，也可以做拦截



事件过滤器















#### 文件 QFile



默认支持utf-8，要打开其他格式的文件

```c++
#include <QTextCodec>
QTextCodec* codec = QTextCodec::codecForName("gbk");
QByteArray arr;
ui->textEdit->setText( codec->toUnicode(arr) )
```



```c++
QString fileName = QFileDialog::getOpenFileName(this,"open file",":/");
    ui->lineEdit->setText(fileName);
    QFile myFile(fileName);
    myFile.open(QIODevice::ReadOnly);
    QByteArray arr;
    while( !myFile.atEnd() )
    {
        arr += myFile.readLine();
    }

            //myFile.readAll();
    ui->textEdit->setText(arr);
    myFile.close();

    myFile.open(QIODevice::Append);
    myFile.write("aaaa");
    myFile.close();
```

文件信息



```
#include <QFileInfo>


```







```c++
void mousePressEvent(QMouseEvent *e);
void mouseReleaseEvent(QMouseEvent *e);


void MyPtn::mousePressEvent(QMouseEvent *e)
{
    if(this->pressImgPath != "")
    {
        QPixmap pix;
        bool ret = pix.load(this->pressImgPath);
        if(!ret)
        {
            qDebug()<<"error";
        }
        this->setFixedSize(pix.width(),pix.height());

        this->setStyleSheet("QPushButton{border:0px;}");
        this->setIcon(pix);
        this->setIconSize(QSize(pix.width(),pix.height()));
    }
    return QPushButton::mousePressEvent(e);
}
```



