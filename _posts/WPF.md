---
title: WPF基础学习
tags: WPF
categories: 编程
---
<font face="微软雅黑"> </font>
<center>WPF基础知识学习</center>

<!-- more -->
<center> [本文地址](https://tiandaochouqin1.github.io/WPF/) </center>

1. WPF开发环境的使用
事件窗口与属性窗口；
事件重用；
2. sender 是什么

3. 继承与类型转换
所有对象都直接或间接继承自Object对象； 继承用冒号表示；
父类变量可以指向子类对象； 子类变量指向父类对象需要类型转换。
*is as 里氏替换原则

4. 集合类List
泛型集合；
foreach遍历；
List 灵活、效率不高。
Add/Remove/Clear
List<string> lst1 = new List<string>();

5. Xaml文件的格式
手写XAML。
XML,大小写敏感
两种赋值方法： Text=“00”或<Text>oo</Text>
<Button/>自闭合 
或<Button></Button>

6. 控的常用通用属性
* visibility: 枚举类型
* IsEnabled
* Backgroud/Foreground
* FontSize

7. TextBox
MaxLength
textWrapping
PasswordBox：属性为Password

8. CheckBox与可空（NULL）数据类型
 bool、datetime、int等值类型不可为null，但是在类型后加"?"则为可空类型。bool？转换为bool需要强制转换。
 CheckBox.IsChecked 是bool？类型

9. RadioButton、Picker、Image、ProgressBar控件
RadioButton：分组
DatePicker：now /today
Image: source
ProgressBar: value/IsIndeterminate

10. 页面布局
即控制子控件的位置、大小的控制。
StackPanel：层层嵌套

11. Grid布局
RowDefination/ColumnDefination
为控件分配行列
HorizonAlignment/VerticalAlignment
Margin

12. 布局的嵌套
容器之间互相嵌套

13. 菜单Menu
上下文菜单与主控制菜单
MenuItem、  Header

DockPanel：位置-上、下、左、右。优先级

14. ToolBar
工具条控件，放入其中的控件都有新的默认外观。
显示图标：Button的Content中放Image

15. 多窗口基础
* 默认启动窗口：StartupUri=
* Title
* 不能修改大小：ResizeMode
* 窗口显示位置:WindowStartupLocation
* Height/Width 默认最大化:Windowstate

子窗口：先new一个窗口获得窗口的新实例，调用**ShowDialog**显示；
窗口之间传递数值：public属性设置变量

**DialogResult:**只要设置了DialogResult属性（true或false），窗口就会退出，ShowDialog()的返回值就是DialogResult，然后通过判断DialogResult是true还是false来确定用户意愿。



16. 子窗口返回属性
public

17. Input窗口实例
DialogResult值=ShowDialog()返回值 bool？类型

18. 打开、保存文件对话框
SaveFileDialog、 OpenFileDialog
FileName
InitialDirectory

picture.Source = new BitmapImage(new Uri(picName));



