## Outline
* Introduction
    * Description of a pattern
    * Features of the book, notations 
    * Roles of design pattern
* A case study: designing a document editor
    * Composite pattern (document data)
    * Strategy pattern (document formatting)
    * Decorator pattern (interface elements)

* The concept of design pattern
> “Each pattern describes a problem which occurs over and over again in our environment, and then describes the core of the solution to that problem, in such a way that you can use this solution a million times over, without ever doing it the same way twice”
每个模式都描述了一个在我们的环境中反复出现的问题，然后描述了这个问题的解决方案的核心，这样你就可以将这个解决方案反复使用一百万次，而不用重复做同样的事情。

* 使得代码更通用：设计模式目标之一
![](/images/2019年4月6日/2019-04-06_192345.png)



```
#include <iostream>
#include <vector>
#include <list>
#include <math.h>
using namespace std;

template <class Container>
void Display_Container(Container c)
{
	Container::iterator it;
	for (it=c.begin(); it!=c.end(); it++)
		cout << *it << " ";
	cout << endl;
}
template <class Container>
void Sort(Container & c)
{	Container::iterator it1, it2;
	Container::value_type  temp;
		
	for (it1=c.begin(); it1!=c.end(); it1++) {
		for (it2=it1; it2!=c.end(); it2++) {
			if ( *it1 > *it2 ) {
				temp = *it1;
				*it1 = *it2;
				*it2 = temp;			
			}
		}	    
	}
}
int main()
{  
	vector <int> v;   
	list <double> l;   

	for (int i=10; i>=0; i--) {
		v.push_back(i);
		l.push_back( sin(i*3.14) );
	}
	Display_Container(v);    
	Display_Container(l);  
	Sort( v );     
	Sort( l );
	Display_Container(v);    
	Display_Container(l);  
	return 0;
}
```

#### Description of a pattern
* Pattern name (singleton)
* The problem
	* Not the basic data structures, nor the entire system
	> 不是基本的数据结构，也不是整个系统
	* Not domain specific
* The solution
* The consequence ( discussion)
	* Space and time trade-offs
	* Language and implementation issues
	* Impact on a system’s  flexibility, extensibility, and portability.
	
#### Roles design patterns play
* Application programs
	* Use design patterns to reduce the class dependencies, platform dependencies, etc.
	> 使用设计模式来减少类依赖、平台依赖等。
* Toolkit
	* Use design pattern for code reuse
	* For example: containers, iterators and algorithms in C++ STL.
* Frameworks
	* Emphasize design reuse
	* Use of patterns in a framework makes learning the usage of the framework easier
	> 在框架中使用模式使学习框架的使用更加容易

#### When not to use design patterns
* Drawbacks of using design patterns
	* Complicate the design
	* Degrade the performance of the system, in terms of space and time requirements.
	> 在空间和时间方面降低了系统的性能。
* Only use design patterns if you want:
	* Flexibility
	* Extensibility
	* portability(可移植性)

## A case study: designing a document editor
#### Design problems
* A WYSIWYG document editor
* Design problems
	* Document structure
	* Formatting
	* Embellishing the user interface
	> 美化用户界面
	* Multiple look-and-feel standards
	> 多种外观标准
	* User Operations
	* Spelling checking and hyphenation(连字符)

#### Document structure
![](/images/2019年4月6日/2019-04-06_201054.png)
> The document has a tree-like structure
这些元素跟用户交互时有一些相同的行为。因此在构想的时候，不仅仅考虑数据方面，更要考虑行为相似性上。

#### Features
* A substructure may contain other substructures (recursive).
> 子结构可以包含其他子结构(递归的)。
* Text and graphics are treated uniformly.
> 文本和图形被统一处理。

	* A figure can be embedded into a line of text
	> 图形可以嵌入到一行文本中
	* Characters can be embedded into a figure
	> 字符可以嵌入到图形中
* Single element and a group of elements are treated uniformly (move, set font, etc).
> 单个元素和一组元素被统一处理(移动、设置字体等)。

![](/images/2019年4月6日/2019-04-06_201510.png)
* The abstract class: a common interface
> 抽象类:公共接口
* The aggregation association: describes the tree
> 聚合关联:描述树

> Intersects：判断是否和左边的元素交叉。
以上这种模式叫做composite
第二行前三个只能是leaf，最后一个可以是中间层

#### The common interface


```
class Glyph {
public:
	virtual void Draw( Window * )=0;
    virtual void Bounds( Rect & )=0;
	virtual bool Intersects( const Point &)=0;
	virtual void Insert(Glyph *, int)=0;
    virtual void Remove(Glyph *)=0;
    virtual Glyph * Child (int)=0;
	virtual Glyph * Parent( )=0;
}
```

#### The composite pattern
* Applicability（适用性）
	* Part-whole hierarchies
	> Part-whole层次结构
	* Single object and composite object are treated uniformly
	> 对单目标和复合目标进行统一处理
* Structure
* Participants
	* Component
		* common interface
		* default behaviors
		* accessing and managing its child components
		* Accessing a component’s parent

![](/images/2019年4月6日/2019-04-06_202759.png)
> 共同的结构为operation()

#### Composite pattern
* Collaborations:
	* The composite object iteratively call the leaf’s operation
	> 复合对象迭代地调用叶子的操作
* Consequences:
	* Extensibility: it is easier to add new kinds of components
	> 可扩展性:添加新的组件更容易
	* Disadvantage: it is hard to constraint the composite objects such that they can only include certain kinds of components.
	> 缺点:很难约束组合对象，使它们只能包含某些类型的组件。

#### Composition and compositor
> Composition可以理解为作品，compositor可以理解为作者
* Composition
	* Initially, it contains the un-formatted objects (characters, figures, etc).
	> 最初，它包含未格式化的对象(字符、图形等)。
	* The formatting algorithms insert structural objects ( Rows, Columns, etc) into it.
	> 格式化算法将结构对象(行、列等)插入其中。
* Compositor
	* Different formatting algorithms are encapsulated into the class
	> 不同的格式化算法被封装到类中
	* Interface
		* void SetComposition ( Composition* )
		* virtual void Compose( )

![](/images/2019年4月6日/2019-04-06_205750.png)
> 简单排版：速度比较快
被处理的对象和处理策略分离开。使得可以动态切换策略

#### Strategy pattern
* Applicability
	* You need different variants of an algorithm.
	> 你需要一个算法的不同变体。
	* Some algorithms use data that clients should not know about.
	> 有些算法使用客户端不应该知道的数据。
	* Essential point: separator the algorithms from the data.
	> 要点:将算法与数据分离。
* Structure

* General structure of the strategy pattern
![](/images/2019年4月6日/2019-04-06_210129.png)

![](/images/2019年4月6日/2019-04-06_211956.png)
> Notes:
Sub-classing the context class can alsoimplement different algorithms
>> 子类化上下文类还可以实现不同的算法

> Drawback: you can not easily change the algorithms dynamically.
从A切换到B就没那么容易，被处理的数据被蹂在一起。所以没办法在运行阶段转换策略



```
void Composition::Compose() {
	switch ( _breakingStrategy ) {
	case SimpleStrategy:
		ComposeWithSimpleStrategy();
		break;
	case TexStrategy:
		ComposeWithTexStrategy();
		break;
	// ...
	}
}
// With strategy pattern
void Composition::Compose() {
	_compositor->Compose();
	// ...
}
```

#### Decorator pattern
* Intention
“Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to sub-classing for extending functionality.”
> 动态地为对象附加额外的职责。装饰器为扩展功能提供了一个灵活的子类化替代方案。

* It is some individual objects (not an entire class) to which the functionality is extended. E.g, borders, scrolling bars.
> 将功能扩展到某些单独的对象(而不是整个类)。例如，边框，滚动条。

#### Solutions for appending additional functionalities
-- by class inheritance
![](/images/2019年4月6日/2019-04-06_213019.png)
* Drawbacks:
	* You can not extend or remove the functionality at run-time. 
	* If there are many decorative functionalities, number of subclasses will explode.
	> 如果有许多装饰功能，子类的数量将会暴增。

#### Solutions for appending additional functionalities
-- by mixin class
Solutions for appending additional functionalities
-- by mixin class
![](/images/2019年4月6日/2019-04-06_213133.png)
* Drawbacks: 
	* similar to that of the solution of class inheritance

#### Solutions for appending additional functionalities
-- by class composition (#1)
![](/images/2019年4月6日/2019-04-06_213221.png)
* Drawbacks:
	* Interface of the two classes are different
	* Difficult to recursively decorate

#### Solutions for appending additional functionalities
-- by class composition (#2)
![](/images/2019年4月6日/2019-04-06_213317.png)
* Drawbacks: 
the Basic_class must take care of the interface elements

#### Decorator pattern
* The basic class is enclosed by a decorator class
> 基本类由修饰类包围
* The decorator class conforms to the interface of the basic class.
> 装饰器类符合基本类的接口。
* Implementation of the interface:
	* Forwards requests to the basic class
	> 将请求转发给基本类
	* Perform any additional actions before or after the forwarding
	> 在转发之前或之后执行任何其他操作
* Consequence: decorators can be nested recursively.
> 结果:装饰器可以递归嵌套。

#### Application of the decorator pattern to the example
![](/images/2019年4月6日/2019-04-06_213801.png)

#### An example: object diagram
![](/images/2019年4月6日/2019-04-06_213814.png)
![](/images/2019年4月6日/2019-04-06_213849.png)

#### General structure of the decorator pattern
![](/images/2019年4月6日/2019-04-06_213908.png)

#### Applicability
* To add or remove responsibilities at run-time
> 在运行时添加或删除职责
* The component class does not have to know anything about its decorators.
> 组件类不需要知道关于它的装饰器的任何信息。
* Comparison between decorator pattern and composite pattern
	* Decorator pattern is a degenerate composite pattern
	> 装饰器模式是一种退化的组合模式
	* Intentions are different
	> 意图是不同的
	
		* Decorator: for adding additional functionalities
		> 修饰符:用于添加附加功能
		* Composite: for object aggregation
		> 组合:用于对象聚合

#### Implementation
Keeping the component class lightweightOtherwise, the decorators will be too heavyweight  to use
> 如果保持组件类的轻量级，装饰器将过于重量级而无法使用

#### An example
* I/O stream class
	* Basic functionality: convert objects into bytes to store into a file or a memory block.
	> 基本功能:将对象转换为字节存储到文件或内存块中。
	* Additional functionalities:
		* Data compression
		* Convert to 7-bit ASCII characters
		> 数据压缩
		转换为7位ASCII字符

#### Solution 1: using flags => Fat root class
![](/images/2019年4月6日/2019-04-06_214132.png)
> 缺点:流的所有对象都有属性“bCompress”、“bTo7Bits”，以及特定于数据压缩和数据转换的操作，甚至您想要的是一个纯MemoryStream对象，它没有额外的功能和属性。

#### Solution 2:using decorator pattern
![](/images/2019年4月6日/2019-04-06_214234.png)

#### Code segments to use the decorator pattern
> 使用装饰器模式的代码段



```
Stream * fStream = new FileStream("test.cpp");
Stream * mStream = new MemoryStream();
Stream * fcStream = new CompressingStream(
                        new FileStream("test.cpp") );
Stream * f7Stream = new ASCII7Stream ( new FileStream("test.cpp") );
Stream * fc7Stream = new CompressingStream( 
new ASCII7Stream ( new FileStream("test.cpp") ));

fc7Stream->PutInt(12);
fc7Stream->PutString("hello");
```



#### A Java example


```
import java.io.*;
import java.util.zip.*;

public class GZIPCompress {
  public static void main ( String [ ] args ) throws IOException {
	BufferedReader in = new BufferedReader (new FileReader(args[0]));
	BufferedOutputStream out =
        new BufferedOutputStream (new GZIPOutputStream (new FileOutputStream(args[1])));
	System.out.println("Compressing file...");
	int c;
	while ( (c = in.read()) != -1 )
	    out.write(c);
	in.close();
	out.close();
	System.out.println("Verifying file...");
	BufferedReader in2 = new BufferedReader(new InputStreamReader (
			    new GZIPInputStream (new FileInputStream(args[1]))));
	String s;
	while ( (s = in2.readLine())  != null )
		System.out.println(s);
  }
}
```
