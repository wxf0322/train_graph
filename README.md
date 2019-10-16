# `pyETRC`列车运行图系统

## 概述

本项目是基于Python语言和PyQt5的非官方性质、简易的中国铁路列车运行图系统。本代码的发布遵循`GPLv3`协议。在协议允许范围内，作者保留一切权利和最终解释权。

作者联系方式：mxy0268@qq.com

本项目在Windows 10 操作系统下开发和测试。

### 与`ETRC`的联系

`pyETRC`项目的最初灵感来源和很多功能设置都来自由LGuo等前辈基于java语言开发的`ETRC`列车运行图系统。为致敬开发`ETRC`项目的前辈，本项目定名为**`pyETRC`列车运行图系统**，简称为`pyETRC`。

本系统支持读取和导出`ETRC`列车运行图系统的运行图文件（`*.trc`）。但由于两软件支持的功能有差异，读取和导出过程可能造成一定的信息损失。

## 环境与运行

使用**源代码**方式运行本项目，需要具有以下环境。

1. `Python` 3.7及以上的版本。开发所用的版本是3.7.4.

   > 注：本项目使用了大量的`f-string`语法，该语法在`Python ` 3.6以后的版本才被支持。一些较新的代码中利用了`Python` 3.7中`dict`键值对顺序与添加顺序一致的特性。如果使用3.6.*版本，这部分代码可能出现一些问题。如果使用3.6以下版本，则会报错。

2. 下列的`Python`第三方库，都可以用`pip`安装。

   * `PyQt5`。必须。推荐使用`5.10.1`版本。
   * `xlwt`。可选。在涉及输出`.xls`的操作中需要用到。
   * `xlrd`。可选。在涉及读取.`xls`的操作中需要用到。
   * `xpinyin`。可选。在本系统`2.3.0`版本之前的线路数据库排序中用到。

3. 作者开发的另一支持库`Timetable_new`。该库需要使用github上的源代码安装。

### 第三方库安装

在安装第三方库之前，需要配置好`python`环境，并将安装目录添加到`PATH`环境变量中，安装好`pip`库。相关教程可借助搜索引擎找到。

在shell中依次执行以下命令，无报错即可。

```powershell
pip install PyQt5==5.10.1
pip install xlwt
pip install xlrd
pip install xpinyin
```

### `Timetable_new`的安装

依次执行：

```powershell
git clone https://github.com/CDK6182CHR/Timetable_new
cd Timetable_new
.\install.bat
```

如果不用`git`，也可以在[链接](https://github.com/CDK6182CHR/Timetable_new)中下载并解压源代码，双击执行`install.bat`。

> 注：`install.bat`文件适合windows操作系统。如果是其他操作系统，请自行更改相关代码。

`install.bat`的代码如下。

```powershell
python setup.py build
python setup.py sdist
python setup.py install
pause
```

### 运行

运行`main.py`文件即可。

```powershell
python main.py
```

## 项目结构

这部分作为简单的扩展开发指南。

### 可执行文件

本项目目前有两个可执行文件。

1. `main.py` 是主程序运行入口。
2. `LineDB.py` 在`2.3.0`版本之后添加，是线路数据库维护系统的运行入口。

### 包结构

本项目主要依赖的是`train_graph`包。下面简要说明包结构。

#### 运行图基础数据域

以下类主要用于存储数据，一般不包含与`PyQt`交互的过程，一般涉及文件读写。如果要基于本项目进行扩展开发，**推荐直接调用这些类**。

* `line.py` 铁路线路数据对象。
* `train.py` 列车对象。
* `ruler.py` 标尺（时分标准）对象。
* `forbid.py` 天窗数据对象。
* `graph.py` 运行图对象，主要包括一条线路`Line`和一组车次`Train`。
* `circuit.py` 车底交路对象。每个交路包含若干车次`Train`的序列，每个车次可以属于至多一个交路。
* `route.py` 尚未开发完成的列车运行径路对象。

#### 绘图域

在调用了`PyQt`的模块中，有一部分与底层的绘图（绘制运行线）直接相关，它们是本项目的核心，这里单独列出来。

* `GraphicWidget.py` 核心绘图窗口。继承`QGraphicsWidget`实现，运行图的铺画在本类中完成。
* `trainItem.py` 列车运行线对象。包含了主要的运行线铺画逻辑。是`GraphicWidget`类中的图元。在`2.0.0`版本之后，每一趟列车（一个`Train`对象）允许拥有多个`TrainItem`对象。

#### 停靠面板域

与运行图数据操作相关的类主要有两种形式，一是以**停靠面板**方式，长期显示和有效的面板；二是以对话框为主的操作交互界面。前者包含了本系统的核心操作功能，下面简要说明停靠面板类及其相关类。

* `circuitWidget.py` 交路编辑停靠面板，快捷键为`ctrl+4`。
* `circuitDialog.py` 单个交路数据的编辑对话框，在`CircuitWidget`中发起编辑操作后弹出。
* `circuitDiagram.py` 绘制交路图的面板。在`CircuitDialog`中发起查看交路图操作后产生。
* `circuitDiagramWidget.py` 绘制交路图的对话框。
* `colorWidget.py` 默认颜色设置面板。在`1.4.0`之后的版本中，被集成到`运行图设置`面板中。
* `configWidget.py` 运行图设置面板。包含关于运行图铺画比例、运行图备注等设置。快捷键为`ctrl+G`
* `currentWidget.py` 当前车次编辑面板。包含对当前选中的列车的车次、运行线管理、时刻表编辑等功能。这是最集中的修改列车信息的面板。快捷键为`ctrl+I`。
* `forbidWidget.py` 天窗编辑面板。快捷键为`ctrl+1`。
* `interactiveTimetable.py` 交互式时刻表。与时刻表面板布局类似，但支持更改列车时刻表， 且更改**立即生效**，立即体现在运行图上。
* `lineWidget.py` 线路编辑面板，可编辑线路名称和站表。快捷键为`ctrl+X`。
* `rulerWidget.py` 标尺编辑面板。可管理标尺（增删改）。标尺数据属于线路数据的一部分，但其编辑功能是独立的。快捷键为`ctrl+B`。
* `trainTimetable.py` 当前车次时刻表面板。将当前车次时刻表以只读方式、占用宽度尽量小的形式展示，建议可以长期开启的停靠面板。快捷键为`ctrl+Y`。
* `trainWidget.py` 车次列表面板。展示基本的车次信息，并可增删。快捷键为`ctrl+C`。
* `typeWidget.py` 显示类型面板。提供简洁的界面来快速选择显示或不显示某一类列车的运行线。快捷键为`ctrl+L`。
* `typeDialog.py` 是由`ConfigWidget`调用的列车类型管理对话框。管理默认情况下用于识别列车种类的车次正则表达式，是否为客车。



#### 操作窗口域

其他文件名形如`*Dialog.py` `*Widget.py`的大多数是与操作相关的窗口。下面简单说明。

* `mainGraphWindow.py` 主窗口类。程序启动后首先产生本类实例。
* `batchChangeStationDialog.py` 批量调整站名，快捷键为`ctrl+shift+U`。
* `changeStationDialog.py` 修改站名。快捷键为`ctrl+U`。
* `changeTrainIntervalDialog.py` 列车区间时刻表重排功能。快捷键为`ctrl+shift+R`。
* `correctionWidget.py` 提供对当前车次时刻表重新排序的功能。快捷键为`ctrl+V`。
* `detectWidget.py` 根据标尺，推定列车区间通过时刻。快捷键为`ctrl+2`。
* `exchangeIntervalDialog.py` 区间换线。交换两车次指定区间的运行线。快捷键为`ctrl+5`。
* `helpDialog.py` 内置的简明功能表。快捷键为`F1`。
* `importTrainDialog.py` 导入车次对话框。快捷键为`ctrl+D`。
* `intervalCountDialog.py` 区间对数表。快捷键为`ctrl+3`。
* `intervalTrainDialog.py` 区间车次表。快捷键为`ctrl+shift+3`。
* `intervalWidget.py` 当前车次区间性质计算。支持计算选定区间的停站次数，旅速等。快捷键为`ctrl+shift+Q`。
* `rulerPaint.py` 标尺排图向导类。按标尺，指定开始站点和方向、各站停站时长，自动铺画运行线。这是本系统亮点功能之一。快捷键为`ctrl+R`。
* `stationTimetable.py` 车站时刻表，快捷键为`ctrl+E`。
* `stationvisualize.py` 车站时刻表可视化。根据车站时刻等信息，提供一种股道安排方案并可视化。
* `trainComparator.py` 两车次时刻对照。快捷键为`ctrl+shift+W`。
* `trainDatabase.py` 为导入车次功能中的相关设定提供支持。功能快捷键为`ctrl+D`。



#### 其他杂项

* `trainFilter.py` 车次筛选器。在全局用快捷键`ctrl+shift+L`启动“高级显示车次设置”时，会调用本类。在车次编辑、车站时刻表等处也有使用。
* `thread.py` 与多线程有关的部分。目前仅用于处理“车次事件表”（`ctrl+Z`）。