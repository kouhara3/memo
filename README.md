# memo

http://terasolunaorg.github.io/guideline/5.1.0.RELEASE/ja/ImplementationAtEachLayer/DomainLayer.html#service




1、丢掉鼠标吧之Eclipse快捷键篇

1.1文件切换的三种方式

1.1.1  Ctrl + E,在右边显示出当前打开的所有文件

1.1.2 Ctrl + Pg Up ,Ctrl + Pg Dn ,逐个文件跳跃

1.1.3 Ctrl + F6，在当前文件里显示当前打开的所有文件


1.2  复制当前行或者当前选中的代码块

在Eclipse里面，如果需要复制一行，无需使用Ctrl + C，只需要使用Ctrl + Alt + ↓。
注意这个快捷键通常会有冲突，可以在keys的面板上自行修改。我自己喜欢用Ctrl + Alt + End来替换Ctrl +Alt + ↓。

public class TestString {
 
   public static void main(String[] args) {
 
      String s = "";
 
      System.out.println(s == null);
 
   }
}

1.3  显示当前类的所有属性和方法

假设你想复制“String s = “””这一行，只需把焦点定位到它所对应的行，然后按下Ctrl + Alt + ↓即可。这个快捷键也支持复制代码块。

以前都是用Eclipse的outline来看，现在你可以关闭它了，因为使用Ctrl + O就可以显示当前文件的所有属性和方法。注意是大写字母O,不是数字0。



1.4  Ctrl + W关闭当前文件，Ctrl + Shift + W关闭所有文件

使用Ctrl + W可以关闭当前文件，Ctrl + Shift +Ｗ可以关闭当前打开的所有文件。

要是我关闭当前文件后，后悔了，想重新打开，咋整。使用Alt + ← 即可。

1.5 快速定位当前文件在工程中的位置,即在package视图中的位置

一种方法是直接点击package视图中的”Link with Editor”。



但是这种方式有个缺点，就是必须使用鼠标去点击”Link with Editor”。

第二种方式是使用Alt + Shift +W,显示视图，然后选择package视图，同样可以定位到当前文件在package视图中的位置，这样就避免使用了鼠标。


1.6  选中Java文件中的内容

public String get(@RequestParam(value = "key") String key) {
 
    Object obj = osCacheService.get(key);
 
    if (obj != null) {
 
      return JsonUtil.toJson(obj);
 
    }
 
    return"";
 }

这个快捷键非常好用，如果你想选择if语句中的所有内容，也可以使用这个快捷键，以此类推。假设你把鼠标定位在osCacheService上，那么可以使用Alt + Shift +→,选择osCacheService整个字符串，而无需使用鼠标来操作。

1.7  Ctrl + T选择java文件,Ctrl + R选择资源文件

如果你只是想找java类，那么直接使用Ctrl + T效率比较高。

1.8  Ctrl + H 进行全局搜索和局部搜索

1、 除了”FileSearch”之外，其他可以从视图中移除

使用Ctrl + H调出搜索视图后，点击Customize。





只保留”File Search”选项，其他选项用处不大。

2、 局部搜索

可以选择视图中的



进行局部搜索，从而提高效率。

1.9  自动生成get/set方法、toString方法、hash方法

1、 使用Alt + Shift+S 调出代码生成视图




2、 调出 视图后、直接输入r调出get/set生成视图,输入h调出hash方法生成视图，输入s调出toString方法生成视图

2.0  视图切换Ctrl + F7

这个快捷键也是相当实用，比如说在代码视图中，你想看看Console中内容，Ctrl + F7可以帮到你。



再比如说，你想启动tomcat，只需按下Ctrl + F7,切换到Servers视图，然后按下Ctrl + Alt + D即可，完全无需使用鼠标的。

2.1  经典的Quick Access

输入Ctrl + 3,就可以使用Quick Access了。比如说，你想为Eclipse配置Maven环境，通常你都需要点击WindowàPreferencesàMaven,有了Quick Access，你只需要直接输入Maven即可。

1、 使用Ctrl + 3

2、 输入您需要在Eclipse查找的东东的关键字



2.2  变量名和方法之间的跳跃

当你想知道当前方法的下一个方法是哪个的时候，只需按下Ctrl + Shift + ↓即可达到目的，这个快捷键同样适用于变量名。

2.3  重构

1、 修改名字

假设你要修改方法的参数名字、或者类变量、方法变量，同时这些变量又被很多代码引用着，那么你可以直接使用Alt + Shift + R 统一修改一次即可。

把焦点定在变量名上，然后使用Alt + Shift + R，此时Eclipse会提示你输入新的变量名，点击回车就可以修改成功了。此快捷键同样也适用于修改类名和方法名。

2、 抽取类方法

如果一个方法的代码太长，你想抽取子方法，那么只要选中某块代码，然后使用Alt + Shift + M,就会弹出抽取视图，输入方法名即可。



2.4   Eclipse的面包屑功能Alt + Shift + B

Eclipse的面包屑功能类似于package视图功能，只需按下Alt+ Shift + B,就可以在Eclipse中显示面包屑。


熟练使用这个工具的话，就不需要package视图了，直接利用面包屑的导航功能即可。在这里顺便介绍一下，如何最快的拷贝一个类的名字。

1、 在当面类文件中，使用Alt+ Shift + B调出Eclipse面包屑

2、 然后点击回车，Eclipse会自动选中类的名字的

有部分朋友不喜欢这个功能，那你可以通过点击Eclipse中工具栏中的Toggle BreadCrumb按钮，即可取消面包屑。



2.5  Ctrl + Shift +G,找出调用某个方法的所有类

有时候，你想查看某个java方法被哪些类方法调用了，可以直接使用Ctrl + Shif + G。


以getAdOrderById这个方法为例子，鼠标定位到这个方法上，使用Ctrl + Shif + G。


Search视图上会列出调用getAdOrderById方法的所有类

2.6  Ctrl + K 和Ctrl + Shfit + K,查找文本

当你选中某个文件中的某些字符串时，你可以使用Ctrl + K向下寻找相同的字符串，使用Ctrl + Shfit +ｋ向上寻找相同的字符串。

2.7  Ctrl + F，调出搜索文本的视图

在这个视图上，有一些选项，用于过滤信息，例如，大小写敏感。。


这个视图这么多选项，如何避免使用鼠标来点击呢。细心的朋友应该可以看到，每个选项的文字上都有某个字母下面有下划线，例如：



，字母C下面有下划线，这个时候，你只需借助Alt键即可选中，例如、Alt +Ｃ就可以选中”Case sensivive”选项。

还有另一个办法，就是使用Tab键，没按一次，就会选择一个选项。

2.8  不用鼠标如何关闭打开的视图

如果你打开了某个视图，例如Ctrl + F,Ctrl + H,打开搜索视图后，你想关闭它。可以直接使用Esc键，避免使用鼠标。

2.9  复习一下，代码修改完后，想启动tomcat，用快捷键如何操作

1、 使用Ctrl + F7切换到Servers视图

2、 使用Ctrl + Alt+ D启动tomcat。

3.0  如何快速生成for循环、switch 等代码块

例如说，你想编写一个switch代码块，只需输入switch，然后使用Alt + /,Eclipse就会生成如下代码：


注意在比较新的Eclipse版本中，Alt + /是默认的代码提示快捷键，相对旧版的默认快捷键是Ctrl + Space,它会跟输入冲突。如何修改Ctrl + Space这个默认快捷键呢？

注意使用前面的知识点，是可以通过快捷键来操作的。

1、 Ctrl + 3

2、 输入keys，跳转到keys视图



3、 跳转到keys视图后，默认选中的选项是 ,如何跳转到，从而输入内容呢。利用Tag键即可。

然后输入content assist,

4、 使用Alt + B,跳到Binding选项，输入Alt+/即可。

5、 使用Tag键，跳到OK选项，单击回车键即可生效

3.1  大小写转换

Ctrl + Shift + Y 变小写,Ctrl +Shift + X变大写。

3.2  创建各种类型的文件

例如要在当前文件所在的包中，创建一个新的类，可以使用Alt + Shift + N,调出创建视图，选择相关类型即可。


想在任意目录下创建的话，使用Ctrl  + N。

3.3  删除当前行

使用Ctrl + D,很实用的快捷键。

3.4  根据左大括号找到右大括号


按下Ctrl + Shift + P后，会跳转到右大括号。

3.5  如何从其他视图中，跳转到代码编辑器视图 F12

当然你可以通过前面提到的Ctrl + F7来进行视图切换，不过如果是切换到代码编辑器视图，直接按下F12即可。

3.6  如何跳转到文件中的特定行,Ctrl + L

Ctrl + L,然后输入行数即可。



3.7 自动生成返回值


createSearchConditionOptions()这个方法的返回值是什么呢？可以使用ctrl + 1调出辅助视图。


然后选择Assign statement to new local variable。Eclipse会自动生成返回值。这是非常实用的操作，基本上天天用。当然你也可以直接使用ctrl+2,Eclipse会右下角弹出一个辅助窗口，然后输入字母L即可。



3.8 显示Eclipse快捷键列表

直接输入Ctrl + shift + l,Eclipse会在右下角弹出快捷键列表。

 

3.9 显示html和xml元素的所有属性

这也是一个相当实用的快捷键。

如果我想看看一个input元素有什么可用的属性，可以使用ctrl+shift+逗号，将属性列表展现出来。



Eclipse快捷键篇介绍完毕。

2、Eclipse实用技巧

2.1 使用bookmark记录代码点

当你在阅读其他人的代码时，可以把代码重要逻辑以bookmark的形式记录起来，方便日后查询。


如果你想把132行记录起来，可以使用鼠标右键，点击”Add Bookmark”,写入一个bookmark。



给bookmark起一个方便记忆的名字后，可以调出bookmark视图查看刚刚记录的代码点。



2.2 调出Eclipse各种视图

例如要调出bookmark视图，可以如下操作：

Window –> ShowView –> Other

这样Eclipse会显示一个show view的视图，然后输入bookmark,显示“Bookmarks”项，点击它即可。



2.3 Console中过长的内容输出到文件中

通常，Eclipse中输出的Console如果太长，你是无法看到所有内容，也不方便进行内容搜索，这个时候你可以把内容输出到文件，进行查看。



假设上面代码输出的内容太长，你可以在common选项中指定输出文件。在当前类文件中选择run configurations



之后，运行代码的时候，内容便会输出到test.log中。

2.4 使用工作集working sets对工程进行归类

通常在一个Eclipse工作空间中会存在非常多的工程，让人眼花缭乱的。这个时候可以使用working sets对project进行归类。

如：


例如我把广告系统的工程放到adsp工作集中。把admin域的代码放到admin工作集中。要做到这一点，第一步是将”Top Level Elements”指定为”Working Sets”，然后点击”Configure Working Sets”进行工作集创建。



2.5  视图分隔

有时候在展现jsp数据的时候，会用到领域模型的字段名称，那么你可以把领域模型和jsp页面分别在两个视图中展现，方便copy。


如果将AppAccess.java在下面显示呢？只需要拖动AppAccess.java即可。当出现如下图的上下分隔线时，放开鼠标即可。



2.6  为Eclipse插件设置快捷键

当你安装完一个Eclipse插件后，可以为其制定快捷键。例如安装一个vim插件，Eclipse工具类中出现了vim图标。





这样按下ctrl+shift+numpad_subtract后，便可调用vim插件了。

2.7 统一Eclipse工作空间的字符编码

 

2.8 改变字体大小


Eclipse使用操作篇也介绍完了。希望通过本文，大家可以更加熟练的使用Eclipse。

