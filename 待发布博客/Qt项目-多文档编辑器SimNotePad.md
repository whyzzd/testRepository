本文将大致介绍SimNotePad的设计思路以及在这个过程中遇到的困难和如何去解决的，对于某些关键点我会详细的说明，源码和打包好的作品见文末链接。

[TOC]

### 一、项目展示

#### 1.界面展示

界面比较简约，默认情况下是没有像Notepad会直接新建一个无名文件，需要手动新建。

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/image-20210705011616102.png" alt="image-20210705011616102"  />

#### 2.功能模块展示(gif动图)

**新建文件**

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/GIF 2021-7-5 1-23-11.gif" alt="GIF 2021-7-5 1-23-11"  />



**打开文件**

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/打开文件.gif" alt="打开文件" style="zoom: 67%;" />



**保存文件**

如果文件未命名那么保存需要指定保存的路径和文件名，如果在已有文件名的基础上保存那么可直接保存至对应的文件。

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/保存测试.gif" alt="保存测试" style="zoom: 67%;" />



**另存为**

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/另存为.gif" alt="另存为" style="zoom: 67%;" />



**编码格式测试**

同一种格式的文件利用不同的格式打开效果不一样，第二次设置编码格式后显示了乱码。

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/编码格式测试.gif" alt="编码格式测试" style="zoom: 67%;" />



**关闭文档测试**

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/关闭文档测试.gif" alt="关闭文档测试" style="zoom:67%;" />



**其它功能展示**

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/其它功能展示.gif" alt="其它功能展示" style="zoom:67%;" />

### 二、结构分析

#### 1.目录结构



<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/image-20210705022947026.png" alt="image-20210705022947026" style="zoom:80%;" />

#### 2.主要的类及对象之间的关系

<img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/image-20210705030738390.png" alt="image-20210705030738390" style="zoom:80%;" />

### 三、源码分析

由于篇幅有限，所以这里就只放了头文件

**mainwindow.h:**

```cpp
#ifndef MAINWINDOW_H
#define MAINWINDOW_H
#include <QMainWindow>
#include<QLabel>
#include"subtext.h"
QT_BEGIN_NAMESPACE
namespace Ui { class MainWindow; }
QT_END_NAMESPACE
class MainWindow : public QMainWindow
{
    Q_OBJECT
public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();
    void init();
    void closeEvent(QCloseEvent *event);
private slots:
    //槽函数看着很多，但是实际写好其中的几个，其它的都差不多了
    void initNewAndOpen(SubText*);
    void doProcessTriggeredByNew(bool);
    void doProcessTriggeredByOpen(bool);
    void doProcessTriggeredByUTF_8(bool);
    void doProcessTriggeredByGB2312(bool);
    void doProcessTriggerByExit(bool);
    void doProcessTriggerBySave(bool);
    void doProcessTriggerBySaveAs(bool);
    void doProcessTriggerByClose(bool);
    void doProcessTriggerByCloseAll(bool);
    void doProcessTriggerByTile(bool);
    void doProcessTriggerByCascade(bool);
    void doProcessTriggerByNext(bool);
    void doProcessTriggerByPrevious(bool);
    void doProcessTriggerByAbout(bool);
    void doProcessTriggerByAboutQt(bool);
    void doProcessTriggerByRedo(bool);
    void doProcessTriggerByUndo(bool);
    void doProcessTriggerByCut(bool);
    void doProcessTriggerByCopy(bool);
    void doProcessTriggerByParse(bool);
private:
    Ui::MainWindow *ui;
    QString codeName;
    QLabel *m_CodeName;
    QLabel *m_CursorPositionLines;
    QLabel *m_CursorPositionCol;
};
#endif // MAINWINDOW_H
```



**subtext.h:**

```cpp
#ifndef SUBTEXT_H
#define SUBTEXT_H
#include <QWidget>
#include<QPlainTextEdit>
#include<QFile>
#include<QMenu>
#include<QMdiSubWindow>
#include<QVector>
//-----------------------------
QT_BEGIN_NAMESPACE
class QPaintEvent;
class QResizeEvent;
class QSize;
class QWidget;
QT_END_NAMESPACE
class LineNumberArea;
//--------------------------------
class SubText : public QPlainTextEdit
{
    Q_OBJECT
public:
    explicit SubText(QWidget *parent = nullptr);

    void NewFile();
    void OpenFile();
    void setMyCode(const QString code);

    void SaveFile();
    void SaveFileAs();

    //文件是否打开成功
    bool fileIsTrOper;
    //是否存在还在编辑的文件
    static bool hasEdit;
    //记录正在编辑的文件个数
    static int nEdit;
    //记录累加的值是0还是1
    int n;
    void closeEvent(QCloseEvent *e);
    void contextMenuEvent(QContextMenuEvent *e) ;
    //重写鼠标事件，当鼠标点击时触发光标位置改变
    void mousePressEvent(QMouseEvent *e);
public slots:
    void dealpaste(QMdiSubWindow *);
signals:
private slots:
    void doProcessContentsChanged();
    //处理粘贴事件
private:
    QString fileName;
    QFile *myfile;
    QString codeName;
    bool isEdit;
    int nonameN;
    //实现按顺序新增未命名编号
    static QVector<bool>v;
    static int number;
//----------------------^------------------------------
public:
    void lineNumberAreaPaintEvent(QPaintEvent *event);
    int lineNumberAreaWidth();

protected:
    void resizeEvent(QResizeEvent *event) override;

private slots:
    void updateLineNumberAreaWidth(int newBlockCount);
    void highlightCurrentLine();
    void updateLineNumberArea(const QRect &, int);

private:
    //指向行号区间的指针
    QWidget *lineNumberArea;
//----------------------v--------------------------------
};

//-----------------------^---------------------------------
class LineNumberArea : public QWidget
{
public:
    LineNumberArea(SubText *editor) : QWidget(editor) {
        codeEditor = editor;
    }

    QSize sizeHint() const override {
        return QSize(codeEditor->lineNumberAreaWidth(), 0);
    }

protected:
    void paintEvent(QPaintEvent *event) override {
        codeEditor->lineNumberAreaPaintEvent(event);
    }

private:
    SubText *codeEditor;
};
#endif // SUBTEXT_H
```



### 四、一些遇到的问题以及解决措施

**在多种情况下关闭的操作逻辑**

尤其是比较特殊的情况，当只要有一个文件未保存，那么程序就不能直接退出，需要等待用户点击相应的按钮，这里使用了全局静态变量进行控制(详情去看源代码)



**一个小细节地方的处理**

可以发现在新建文件时，或者关闭新建文件时(未修改状态)，所有新建文件名后面的序号都是尽可能的变成连续的自然数(如原来有1、2、3三个文件，如果关闭2，再新建一个文件，那么序号是还是2，没经过处理之前新建文件会是4)，这一过程中所用到的方法是引入了一个静态vector容器用来存放对应的序号以及状态。



**集成行号显示和行高亮模块**

阅读源码，调试，集成。



**对象之间通信的问题**

利用静态变量，或者信号与槽，加深了对信号和槽及lambda表达式的理解和使用。



**MDI Area容器默认的显示视图比较难看，想进行调整**

通过查找官方的API文档和使用搜索引擎查找到了对应的函数和用法最后改变了视图显示，当然，其它还有很多地方也是以这种方式。



**UI界面的设计**

这里也是参照NotePad的外观进行设计，但是NotePad不是使用qt开发的，所以只能看看了外观了，很多地方只能自己尝试了，在此过程中也新学了不少的控件属性的设置方法。



### 五、总结

总的来说，这个软件可以满足日常的简单使用，比起windows自带的文本编辑器体验感要好得多，当然，在软件中还存在一些bug，例如可以多次打开同名文件，那么这在实际应用当中是不合理的，另外软件的功能还不够，后续可能还会添加一些新的功能，例如更改字体，字号，增加一些快捷键等等。

代码写的过程当中也苦恼过，尤其是去想新增功能的时候还有修改bug的时候简直头疼，但是当一瞬间的灵感突然涌现以及通过各种分析排除解决bug的时候，那种喜悦是无与伦比的。

[源码链接](https://github.com/whyzzd/SimNotePad)

（欢迎各位给项目pr或者issue哟）