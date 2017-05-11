<h2>《C++ Primer Plus (第6版)》 :books: </h2> 

> [美] Stephen Prata 著    人民邮电出版社
 
```c++
1.4 种让程序访问名称空间 std 的方法：
  (1) 将 using namespace std; 放在函数定义之前，让文件中所有的函数都能够使用名称空间 std 中所有元素。
  (2) 将 using namespace std; 放在特定的函数定义中，让该函数能够使用名称空间 std 中的所有元素。
  (3) 在特定的函数中使用类似 using std::cout; 这样的编译指令，而不是 using namespace std;，
让该函数能够使用指定的元素。
  (4) 完全不使用编译指令 using，而在需要使用名称空间 std 中的元素时，使用前缀 std::，
如：std::cout << "test" << std::endl;
```
