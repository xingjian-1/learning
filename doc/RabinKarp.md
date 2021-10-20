##### RK算法
如果 a = "aaaaaaaaaaaaaaaaab" b="aaab";像这种情况每一轮的检查都需要匹配到b串的最后一个字符，才发现不匹配，两个字符串在每一轮都要比较四次，假设a串长度为m，b串长度为n那么在这种极端的情况下，BF算法的最坏时间复杂度是O(mn)，如何让时间复杂度变为O(n) 有什么更优的解决方案呢？用RK算法呀<br>
RK算法全称Rabin-Karp是由算法的两位发明者Rabin和Karp的名字来命名的。BF算法只是对两个字符串的所有字符依次比较，而RK算法比较的是两个字符串的哈希值。
##### RK算法步骤
1.生成b串的hashcode <br>
2.生成a串中第一个等长子串的hashcode(abb的hashcode) <br>
3.比较两个hashcode <br>
4.a串中第二个等长子串的hashcode(bbc的hashcode) <br>
5.比较两个hashcode <br>
6.生成a串当中第三个等长子串的hashcode(bce的hashcode) <br>
7.比较两个hashcode <br>
8.依次对比，直到找到两个相同的hashcode，然后像BF算法那样，对两个相同hashcode的字符串逐个字符比较，最终判断两个字符串匹配。<br>
##### 代码

            public static void main(String[] args) throws Exception {
                String str = "aacdesadsdfer";
                String pattern = "adsd";
                System.out.println("第一次出现的位置:" + rabinKarp(str, pattern));
            }
            public static int rabinKarp(String str, String pattern) {
                    //主串长度
                    int m = str.length();
                    //b串的长度
                    int n = pattern.length();
                    //计算b串的hash值
                    int patternCode = hash(pattern);
                    //计算主串当中第一个和b串等长的子串hash值
                    int strCode = hash(str.substring(0, n));
                    //用b串的hash值和主串的局部hash值比较。
                    //如果匹配，则进行精确比较；如果不匹配，计算主串中相邻子串的hash值。
                    for (int i = 0; i < m - n + 1; i++) {
                        if (strCode == patternCode && compareString(i, str, pattern)) {
                            return i;
                        }
                    //如果不是最后一轮，更新主串从i到i+n的hash值
                        if (i < m - n) {
                            strCode = nextHash(str, strCode, i, n);
                        }
                    }
                    return -1;
                }
                private static int hash(String str) {
                    int hashcode = 0;
                    //这里采用最简单的hashcode计算方式：
                    //把a当做1，把b当中2，把c当中3.....然后按位相加
                    for (int i = 0; i < str.length(); i++) {
                        hashcode += str.charAt(i) - 'a';
                    }
                    return hashcode;
                }
                private static int nextHash(String str, int hash, int index, int n) {
                    hash -= str.charAt(index) - 'a';
                    hash += str.charAt(index + n) - 'a';
                    return hash;
                }
                private static boolean compareString(int i, String str, String pattern) {
                    String strSub = str.substring(i, i + pattern.length());
                    return strSub.equals(pattern);
                }
##### RK算法缺点
RK算法计算单个子串hash的时间复杂度是O(n),但是由于后续的子串hash是增量计算，所以总的时间复杂度是O(n)。那么RK算法有什么不足之处呢，RK算法的缺点在于哈希冲突，每次hash冲突的时候，RK算法都要对
子串和b串进行逐个字符的比较，如果冲突太多，RK算法就退化成了BF算法。
