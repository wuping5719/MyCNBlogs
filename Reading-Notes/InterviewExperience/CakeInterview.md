* 饼神(孟一凡)面经：

  1.秋招帝都实习7k安全岗(渗透测试)面经:
```html
   渗透测试岗位，面试官问的我有没有代码审计经验，身为php狗的我研究过dedecms代码和phpcms代码.
   还有我们学校各个学院的网站做过友情检测，读过代码很重要，现在国内还是依靠手工挖洞。
   会不会shell编程和Python编程，这个也很重要，还问了我Linux常用操作。
   然后问了问owasp的流程以及POC实现。大概40分钟。

   一般的安全岗位要求的技能真的不少:
     脚本编程：Python和shell必备 PHP代码审计会的话 加分.
     运维技术：Windows+IIS+SQL Server以及Win服务器各种配置 .
              从03到12 Linux安全配置 防火墙 LAMP LNMP体系.
     工具使用Kali Linux MSF Wireshark Fiddler Tcpdump Sqlmap等等.
     SQL注入 xss注入 csrf注入 webshell getshell java序列化漏洞利用.
  真的很多 建议看看知道创宇技能表最新版本.
  
  讲讲我比较熟悉的漏洞挖掘吧.
   (1) Tomcat + JSP; IIS + ASPX; LNAP;LAMP环境搭建必须会.
   (2) XSS和CSRF注入必须会 尤其是DOM XSS Buffer Overflows.
   (3) owasp Top 10 & CWE/SANS Top 25那套流程可以搞.
   (4) 扫描器原理懂 自己可以做出来 现成的扫描器能看懂返回信息.
   (5) 搭建漏洞测试环境 DVWA WebGoat.
   (6) Web手动测试.
   (7) 使用Burp Suite Pro动态修改数据 人工观察返回信息.
   (8) 理解Payload.
   (9) Web代码分析 传统漏洞分析 ASP/ASPX/JSP/PHP源码角度跟踪数据传播.
     解读函数、变量、参数到数据执行.
   (10) C/C++代码纯静态分析 C/C++源码角度跟踪数据执行 依靠外部的数据来控制行为的污染.
     理解缓冲区溢出和输入验证.
   (11) C/C++代码编译分析 深入内存数据 跟踪数据执行流程 理解内存破坏问题.
   (12) 知道常见漏洞和安全知识库 开源软件源码更新版本Patchdiff 危险函数/有漏洞历史的函数.
   (13) IDA pro反汇编 Patchdiff方法 BinDiff二进制补丁比较 .NET->NET Reflector Java -> Java Decompiler.
   (14) 脱壳和动态调试分析.
   (15) beSTORM测试通用协议 会构造Payload来Fuzzing测试.
   (16) 熟悉分析浏览器调用ActiveX控件和Fuzzing测试.
   (17) 熟悉POC实现.
```

 2.启明星辰渗透测试工程师实习面经:
```html
  PHP篇：
     include、require、include_once 区别. 
     超全局数组.
  算法：
     爬虫、宽度优先遍历.
  渗透测试：
     sqlmap原理、内网渗透、xss进入内网、预传输漏洞、文件上传漏洞截断问题、宽字节注入、
     如何定位绝对路径、渗透测试流程，如何入侵百度.
  逆向：
     如何破解注册码.
  工具类：
     你对常用渗透测试工具看法，如何改进，如何收集网上信息，上什么网站.
```

  3.百度测试开发一面:
```html
  (1) C/C++
    虚函数 动态绑定 指针引用.
    如果i=5；那么 a=(++i)--;之后，a和i的值各是多少?
    STL用过吗 vector和List区别？
  (2) SQL 建表 插入数据 索引 分库分表.
  (3) PHP中PDO连接mysql.
  (4) Linux sed命令 找到8080端口占用进程 man和help区别.
  (5) Vagrant Docker使用情况.
  (6) 不调用C++/C的字符串库函数，编写函数 strcpy.
  (7) 最大子数组之和.
  (8) QQ登录测试样例.
  (9) 读了那些书? Unix内核源码剖析 深度理解对象模型 Web全栈工程师自我修养 读书感悟.
  (10) 自动化测试了解.
  (11) 为啥学汇编？
```

  4.十二天实习面试求职之路:
```html
  (1) 全民快乐(9月17日) (机器学习算法工程师)
    内推人及内推过程：全民快乐CEO收的简历，给了面试机会.
    一面：问了Hadoop，自然语言处理知识，项目实现，海量数据处理，两道算法题.
    二面：TCP三次握手，浏览器打开百度访问过程，HTTP实现原理.
    结果：挂掉(都答上来挂了).
    
    面试题：
        二叉树深拷贝.
        修改原二叉树后，如何存储，使得空间最小(遍历修改节点所在路径，即根节点到叶子节点).

  (2) 乐视网(9月18日上午）
      内推人及内推过程：牛客小基友Allen,HR约了面试过程.
      一面:算法题。
      结果:挂掉.
      
     面试题：  
        KMP 算法.
        IP地址.号分割（如223232135124 字符串有多少分割方法）.
        快速排序实现.
        快速幂实现.
      
  (3) 妙计旅行(9月18日)
      内推人及内推过程：直系师哥在妙计工作.
      一面：算法题、遗传算法、增强学习、虚函数、多态、shell脚本编写基本的、SQL语言、
         Linux基本命令（cp、mov、top、grep、sed）、Python、项目、KCP协议实现、libuv、libevent、TCP超时重传.
      结果：当时给了offer.
      
      面试题：
         单词翻转问题(字符串翻转，但是单词内字符串顺序不变).
         反转链表.
         多源最短路径(左老师貌似没讲过，写一下dp思路).
      
  (4) 环信(9月23日)
      内推人及内推过程：直系师姐在环信工作.
      一面：算法题、项目、KCP协议的实现、TCP三次握手、职业规划、实习经历、是否愿意做前端、
         Docker技术、虚拟化技术、异步和同步区别、进程和线程区别、进程切换过程、内存分布、
         Redis使用及实现、B树、B+树、Mysql源码分析.
      二面：职业规划、是否愿意做Golang、Erlang,对并发了解、Redis原理及使用、是否对分布式数据库引擎开发感兴趣.
      三面：职业规划、项目、海量存储、对IM产品了解、SQL语句、Python、
          给了两个加入顶尖团队的机会(分布式数据库引擎、可视化性能调优系统研发)、诚邀我加入环信.
      HR面：性格、以前经历、感情、兴趣、待遇.
      结果：给了offer.
      
      面试题：
         数组中替换空格.
         链表模拟大数加法(反转链表思想).
         桶排序.
         堆排序.
         海量数据找相同，两个好友表找共同好友(哈希).

  (5) 百度凤巢(9月28日)
      内推人及内推过程：某国内大神.
     一面：Kmeans、进程和线程区别、奥赛经历、大学比赛经历、项目、算法题、进程和线程的应用场景、LR分类器、机器学习算法.
     二面：项目、对部门了解、团队协作、算法题、职业规划.
     结果：当时二面面试官以为有三面，回去请示，结果今年百度实习生只有二面，回来说两周之内给通知.
     
     面试题：
        快速排序实现.
        从矩阵最左上角走到最右下角，经历矩阵元素和最小(经典dp).
        单词翻转问题(字符串翻转，但是单词内字符串顺序不变).
```

  5.机器学习笔记——相似性度量:
  <https://www.nowcoder.com/discuss/3453>

  6.c++部分总结:
  <https://www.nowcoder.com/discuss/3455>

  7.POJ_1274_二分匹配题解:
  <http://www.nowcoder.com/discuss/3456>

  8.图形推理题部分整理:
  <http://www.nowcoder.com/discuss/3459>
   图形推理网络资料整理摘自部分公务员考试资料
  <https://www.nowcoder.com/discuss/3460>

  9.c++引用机制:
  <http://www.nowcoder.com/discuss/3505>
  
  10.windows网络编程总结:
  <http://www.nowcoder.com/discuss/3507>

  11.ThinkPHP学习笔记之模板引擎_系统变量:
  <https://www.nowcoder.com/discuss/3508>
  
  12.软件工程那些事#Git,让我们High起来.
  <https://www.nowcoder.com/discuss/3541>
  
  13.算法整理#线段树技巧小结:
  <http://www.nowcoder.com/discuss/3685>

  14.成为Java顶尖程序员 ，看这11本书就够了：
  <http://www.nowcoder.com/discuss/3748>
  
  15.算法导论学习笔记#分治策略:
  <http://www.nowcoder.com/discuss/3898>

  16.iOS安全学习资料汇总:
  <http://www.nowcoder.com/discuss/4078>

  17.KMP算法整理:
  <http://www.nowcoder.com/discuss/4236>

  18.分分钟学会安装linux:
  <http://www.nowcoder.com/discuss/5615>

  19.2016百度之星 资格赛A题 题解:
  <http://www.nowcoder.com/discuss/5827>

  20.《算法设计编程实验》读书笔记之第三章数论的编程实验：
  <http://www.nowcoder.com/discuss/6018>

  21.POJ 2262哥德巴赫猜想：
  <http://www.nowcoder.com/discuss/6020>
  
  22.TCP协议流图:
  <http://www.nowcoder.com/discuss/7736>

  23.缓存系列文章--1.缓存的一些基本常识:
  <http://www.nowcoder.com/discuss/7876>

  24.腾讯云centos6.5搭建python机器学习环境:
  <http://www.nowcoder.com/discuss/12707>

  25.App后台架构剖析:
  <http://www.nowcoder.com/discuss/14624>
  
  26.代发招聘信息：
```html
  (1) 游戏公司暴雪Demonware招聘高级Python工程师:
     http://www.nowcoder.com/discuss/3820
  (2) 河北师范大学车联网研发基地:
     http://www.nowcoder.com/discuss/5623
  (3) 美团内推:
     http://www.nowcoder.com/discuss/5713
  (4) 北京全民快乐科技有限公司招聘:
     http://www.nowcoder.com/discuss/10504
```
