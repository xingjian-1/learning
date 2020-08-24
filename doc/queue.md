#### 队列是什么
###### 队列是一种特殊的线性表，特殊之处在于它只允许在表的前端（front）进行删除操作，而在表的后端（rear）进行插入操作，进行插入操作的端称为队尾，进行删除操作的端称为队头。
#### 队列的特点
###### 它只允许在表的前端（front）进行删除操作，而在表的后端（rear）进行插入操作，进行插入操作的端称为队尾，进行删除操作的端称为队头，队列中没有元素时，称为空队列。队列的数据元素又称为队列元素。在队列中插入一个队列元素称为入队，从队列中删除一个队列元素称为出队。因为队列只允许在一端插入，在另一端删除，所以只有最早进入队列的元素才能最先从队列中删除，故队列又称为先进先出（FIFO—first in first out）线性表。 
#### 队列的原理
###### 顺序队列
###### 建立顺序队列结构必须为其静态分配或动态申请一片连续的存储空间，并设置两个指针进行管理。一个是队头指针front，它指向队头元素；另一个是队尾指针rear，它指向下一个入队元素的存储位置，如图所示每次在队尾插入一个元素是，rear增1；每次在队头删除一个元素时，front增1。随着插入和删除操作的进行，队列元素的个数不断变化，队列所占的存储空间也在为队列结构所分配的连续空间中移动。当front=rear时，队列中没有任何元素，称为空队列。当rear增加到指向分配的连续空间之外时，队列无法再插入新元素，但这时往往还有大量可用空间未被占用，这些空间是已经出队的队列元素曾经占用过得存储单元。顺序队列中的溢出现象：
###### （1） "下溢"现象：当队列为空时，做出队运算产生的溢出现象。“下溢”是正常现象，常用作程序控制转移的条件。
###### （2）"真上溢"现象：当队列满时，做进栈运算产生空间溢出的现象。“真上溢”是一种出错状态，应设法避免。
###### （3）"假上溢"现象：由于入队和出队操作中，头尾指针只增加不减小，致使被删元素的空间永远无法重新利用。当队列中实际的元素个数远远小于向量空间的规模时，也可能由于尾指针已超越向量空间的上界而不能做入队操作。该现象称为"假上溢"现象。
###### 循环队列
###### 在实际使用队列时，为了使队列空间能重复使用，往往对队列的使用方法稍加改进：无论插入或删除，一旦rear指针增1或front指针增1 时超出了所分配的队列空间，就让它指向这片连续空间的起始位置。自己真从MaxSize-1增1变到0，可用取余运算rear%MaxSize和front%MaxSize来实现。这实际上是把队列空间想象成一个环形空间，环形空间中的存储单元循环使用，用这种方法管理的队列也就称为循环队列。除了一些简单应用之外，真正实用的队列是循环队列。 [2] 在循环队列中，当队列为空时，有front=rear，而当所有队列空间全占满时，也有front=rear。为了区别这两种情况，规定循环队列最多只能有MaxSize-1个队列元素，当循环队列中只剩下一个空存储单元时，队列就已经满了。因此，队列判空的条件时front=rear，而队列判满的条件时front=（rear+1）%MaxSize。队空和队满的情况如图：
#### 队列的实现
##### 队列的数组实现
###### 队列可以用数组Q[1…m]来存储，数组的上界m即是队列所容许的最大容量。在队列的运算中需设两个指针：head，队头指针，指向实际队头元素；tail，队尾指针，指向实际队尾元素的下一个位置。一般情况下，两个指针的初值设为0，这时队列为空，没有元素。数组定义Q[1…10]。Q(i) i=3,4,5,6,7,8。头指针head=2，尾指针tail=8。队列中拥有的元素个数为:L=tail-head。现要让排头的元素出队，则需将头指针加1。即head=head+1这时头指针向上移动一个位置，指向Q(3)，表示Q(3)已出队。如果想让一个新元素入队，则需尾指针向上移动一个位置。即tail=tail+1这时Q(9)入队。当队尾已经处理在最上面时，即tail=10，如果还要执行入队操作，则要发生"上溢"，但实际上队列中还有三个空位置，所以这种溢出称为"假溢出"。
      克服假溢出的方法有两种。一种是将队列中的所有元素均向低地址区移动，显然这种方法是很浪费时间的；另一种方法是将数组存储区看成是一个首尾相接的环形区域。当存放到n地址后，下一个地址就"翻转"为1。在结构上采用这种技巧来存储的队列称为循环队列。
      队列和栈一样只允许在断点处插入和删除元素。
      循环队的入队算法如下：
      1、tail=tail+1；
      2、若tail=n+1，则tail=1；
      3、若head=tail，即尾指针与头指针重合了，表示元素已装满队列，则作上溢出错处理；
      4、否则，Q(tail)=X，结束（X为新入出元素）。
      队列和栈一样，有着非常广泛的应用。
      注意：（1）有时候队列中还会设置表头结点，就是在队头的前面还有一个结点，这个结点的数据域为空，但是指针域指向队头元素。
      （2）另外，上面的计算还可以利用下面给出的公式cq.rear=(cq.front+1)/max;
      当有表头结点时，公式变为cq.rear=(cq.front+1)/（max+1）。
##### 队列的链表实现
###### 在队列的形成过程中，可以利用线性链表的原理，来生成一个队列。基于链表的队列，要动态创建和删除节点，效率较低，但是可以动态增长。队列采用的FIFO(first in first out)，新元素（等待进入队列的元素）总是被插入到链表的尾部，而读取的时候总是从链表的头部开始读取。每次读取一个元素，释放一个元素。所谓的动态创建，动态释放。因而也不存在溢出等问题。由于链表由结构体间接而成，遍历也方便。
      1）初始化队列：Init_Queue(q) ，初始条件：队q 不存在。操作结果：构造了一个空队；
      （2）入队操作： In_Queue(q,x),初始条件： 队q 存在。操作结果： 对已存在的队列q，插入一个元素x 到队尾，队发生变化；
      （3）出队操作： Out_Queue(q,x)，初始条件: 队q 存在且非空，操作结果： 删除队首元素，并返回其值，队发生变化；
      （4）读队头元素：Front_Queue(q,x)，初始条件: 队q 存在且非空，操作结果： 读队头元素，并返回其值，队不变；
      （5）判队空操作：Empty_Queue(q)，初始条件： 队q 存在，操作结果： 若q 为空队则返回为1，否则返回为0。