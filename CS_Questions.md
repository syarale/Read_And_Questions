## CS Questions

#### **问题1：strlen 和 sizeof的区别：当参数分别为数组（初始化，未初始化）和指针时？**

注：注意strlen以 '\0' 为结束符

&emsp;

#### **问题2：远程过程调用（RPC）**
* 远程过程调用(RPC)详解：https://waylau.com/remote-procedure-calls/
* 论文：Implementing remote procedure calls

&emsp;

#### **问题3：使用vfork时, 在子进程的main函数中使用return和exit的区别**

1. 参考文献:
   - C语言里，main 函数中 return x和 exit(x) 到底有什么区别?
   - 链接：https://www.zhihu.com/question/26591968
   &emsp;

2. vfork 和 fork的区别：
   - fork 是创建一个子进程，并把父进程的内存数据copy到子进程中；
   -  vfork是创建一个子进程，并和父进程的内存数据share一起用。
&emsp;

3. vfork 产生的原因
   - 产生起初只有fork，但是很多程序在fork一个子进程后就exec一个外部程序，于是fork需要copy父进程的数据这个动作就变得毫无意义，为优化性能，所以搞出了个父子进程共享的vfork。所以，vfork本就是为了exec而生的；
   -  fork的优化，写时拷贝(COW)技术。对于fork后并不是马上拷贝内存，而是只有在需要改变的时候，才会从父进程中拷贝到子进程中，这样fork后立马执行exec的成本就非常小了。而vfork的性能提升变得不明显，且因为共享内存所以比较危险。所以，Linux的Man Page中并不鼓励使用vfork().
&emsp;

4. 使用exit(事实上不能使用exit，应该使用_exit)，不能使用return的原因：
   - 一种说法是，vfork产生的子进程和父进程共用虚拟内存，使用return返回会导致函数栈退栈，使得父进程无法正常进行；
   - 另一种说法是，return不会导致栈空间被修改，只是子进程的寄存器组会被修改。问题出在，当子进程return返回到main()函数的调用点之后，接下来的语句中会有一些别的函数调用，这个时候main()原来的栈内存空间内容被覆盖（主要是返回地址），导致了程序的不确定行为。这种说法更为正确。

&emsp;

#### **问题4：什么是带外数据？**

参考文献：

- https://developer.aliyun.com/article/173533
- https://www.jianshu.com/p/65a4b8c059d4

&emsp;

#### **问题5：select 函数中，maxfd 参数存在的意义？**
- 存在这个参数以及计算其值的额外负担纯粹是为了效率原因。每个fd_set都有表示大量描述符的空间（典型数量为1024，修改这个值需要重新编译内核），然而一个普通进程所用的数量却少得多。内核正是通过在进程和内核之间不复制描述符集中不必要的部分，从而不测试总为0的那些位来提高效率的。（《UNIX网络编程 卷一：套接字联网API》6.3节）
- 所谓的fd_set 实际上是一个位图，将位图中的位置为1，来标记对相应描述符进行监控。系统会对位图所代表的描述符进行轮询。 参考文献：[并发TCP服务器 IO多路复用之select](https://www.codenong.com/cs106871294/) 