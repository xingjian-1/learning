##### 时空复杂度
时间复杂度(运行时间)和空间复杂度(占用空间)是衡量算法好坏的重要指标。时间复杂度用大写的O来表示，具体复杂的程度用括号里面的常量或者对数函数表示。
##### O(1)
最低复杂度，耗时/耗空间与输入数据大小无关，无论输入数据增大多少倍，耗时/耗空间都不变。哈希算法就是典型的O(1)时间复杂度，无论数据规模多大，都可以在一次计算后找到目标（不考虑冲突的话）。
##### O(n)
数据量增大几倍，耗时也增大几倍，比如遍历算法，要找到一个数组里面最大的一个数，就要把n个变量都扫描对比一遍，操作次数为n，算法复杂度是O(n)。例子：100把钥匙贴上编号，用绳子串起来，要找出10号房间的钥匙，就需要一个一个去查找，钥匙越多，查找的次数就越多。
##### O(n^2)
n的2次方，表示数据量增大n倍时，耗时增大n的平方倍。比如冒泡排序排一个数组要双层循环，对于n个变量的数组，需要交换变量位置n^2次（n乘以n次），算法复杂度就是O(n^2)。例子：有100层楼每层有100个房间，把每层100个房间钥匙用绳子串起来，那就是100个钥匙串，要拿到某一把钥匙，就要先找到哪一层楼，再找到这一层钥匙的编号。
##### O(log n)
当数据量增加n倍时，耗时只增加logn倍(log是以2为底的，当数据量增加256倍时，耗时只增加8倍)。二分查找也叫折半查找，每找一次排除一半的可能，256个数据中查找只要找8次就可以找到目标。它的复杂度是O(log n)。
##### O(n log n)
就是n乘以log n，当数据增大256倍时，耗时增大256乘以8=2048倍。归并排序就是O(n log n)的时间复杂度。例：IO次数取决于B树的高度h，假设当前数据表的数据为N，每个磁盘块的数据项的数量是m，则有h=㏒(m+1)N，即N=(m+1)h,当数据量N一定的情况下，m越大，h越小。m=磁盘块的大小/数据项的大小，磁盘块的大小大概是一个数据页的大小，固定的，数据项占的空间越小，单个磁盘块容纳的数量越多，树的高度越低