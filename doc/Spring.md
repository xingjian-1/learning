#### OOA
Object-Oriented Analysis（面向对象分析方法）是确定需求或者业务的角度，按照面向对象的思想来分析业务。例如：OOA只是对需求中描述的问题，进行模块化的处理，描述问题的本质，区别每个问题的不同点相同点，确定问题中的对象。强调的是在调查资料的基础上，针对OO方法所需要的素材进行的归类分析和整理，而不是对管理业务现状和方法的分析。
#### OOD
面向对象设计（Object-Oriented Design，OOD）方法是OO方法中一个中间过渡环节。其主要作用是对OOA分析的结果作进一步的规范化整理，以便能够被OOP直接接受。是一种软件设计方法。按照Bjarne Stroustrup的说法，面向对象的编程范式（paradigm）是：
* 决定要用的类；
* 给每个类提供完整的一组操作；
* 明确地使用继承来表现共同点。
由这个定义，我们可以看出：OOD就是“根据需求决定所需的类、类的操作以及类之间关联的过程”。
OOD的目标是管理程序内部各部分的相互依赖。为了达到这个目标，OOD要求将程序分成块，每个块的规模应该小到可以管理的程度，然后分别将各个块隐藏在接口（interface）的后面，让它们只通过接口相互交流。比如说，如果用OOD的方法来设计一个服务器-客户端（client-server）应用，那么服务器和客户端之间不应该有直接的依赖，而是应该让服务器的接口和客户端的接口相互依赖。
这种依赖关系的转换使得系统的各部分具有了可复用性。还是拿上面那个例子来说，客户端就不必依赖于特定的服务器，所以就可以复用到其他的环境下。如果要复用某一个程序块，只要实现必须的接口就行了。
OOD是一种解决软件问题的设计范式（paradigm），一种抽象的范式。使用OOD这种设计范式，我们可以用对象（object）来表现问题领域（problem domain）的实体，每个对象都有相应的状态和行为。我们刚才说到：OOD是一种抽象的范式。抽象可以分成很多层次，从非常概括的到非常特殊的都有，而对象可能处于任何一个抽象层次上。另外，彼此不同但又互有关联的对象可以共同构成抽象：只要这些对象之间有相似性，就可以把它们当成同一类的对象来处理。
#### OOP
面向对象编程（Object Oriented Programming，OOP，面向对象程序设计）是一种计算机编程架构。OOP核心思想：封装，继承，多态。所谓面向对象就是基于对象概念，以对象为中心，以类和继承为构造机制，来认识、理解、刻画客观世界和设计、构建相应的软件系统。
Java语言的出现使其oop思想可谓是发扬光大。Python 和 Ruby 是最近才出现的语言，在开发时完全采用了 OOP 的思想，而流行的 Perl 脚本语言从版本5开始也慢慢地加入了新的面向对象的功能组件。用组件代替“现实”上的实体成为 JavaScript（ECMAScript） 得以流行的原因，有论证表明对组件进行适当的组合可以让OOP 达到软件工程的三个主要目标：重用性、灵活性和扩展性。
对象的产生：

一、是以原型（prototype）对象为基础产生新的对象。

二、是以类（class）为基础产生新对象。
