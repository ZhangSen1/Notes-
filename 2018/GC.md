## 内存分配
* 线程堆栈    
  *  值类型
  *  高  -> 低
* 堆 
  * 低 -> 高 
  * GC 堆 
    * 小于 85000 字节
    * 垃圾回收时会压缩
  * LOH堆
    * Large Object Heap
    * 大对象堆
    * 不会压缩
    * 讨论垃圾回收不用考虑(只有完全垃圾回收时被回收)  

## 垃圾回收
* 何为垃圾
  *  不可达对象
  *  应用程序根(指针)
     * 创建可见对象图 
     * GC 不会让一个对象在对象图中出现两次(避免循环引用计数)  
 * 回收
   * 从内存中清除
   * 压缩堆
   * 更改指针引用 
   * 终结器(析构方法?)
* 代
  *  效率高
  *  0 代, 1代, 2代 容量动态分配
  * .net 4.5 最多三代 
  * 0代 (暂时代)
    * 从来没有被标记回收
  * 1代 (暂时代)
    * 上一次垃圾回收没有被回收
  * 2代
    * 仍没有被回收
 * .NET 3.5及之前:
   * 并发垃圾回收:
     * 回收暂时代时观其当前进程中所有的线程(确保不会在回收过程中访问托管堆)
     * 感觉不到中断 (高度优化)
     * 专门线程回收非暂时代中对象(可以继续在对上分配对象) 
* .NET 4.0 及之后:
  * 后台垃圾回收
    * 仅回收非暂时代对象
    * 减少给定线程参与垃圾回收挂起的时间  
* 何时回收
  * 第0代满时
  * GC.Collect
  * 内存不足时
  * 卸载 AppDomain时 (所有代) 
  * etc
* 强制回收
  * 仅当非托管 
  * 极特殊托管类型 (Drawing  etc)
  * 终结器 (析构函数?) 
    * 不可调用 不可重载
    * 只可处理非托管
    * 调用无顺序,时间不确定
    * 可能不调用
    * 垃圾回收时自动调用
    * 会造成当前代增加
      * 垃圾回收如果有显示实现 Finalize 的对象，会被标记成可终结的, 会放到终结队列中,由垃圾回收器维护，并复制到一个终结可达表(freachable), 在下一次垃圾回收时会产生另一个线程, 并调用终结可达表中对象的 Finalize()
      * 所以真正删除一个对象,最少要两次垃圾回收
  * IDisposable
    *释放? 处置? 清理? *
    * 实现 Dispose()
    * 显示调用
    * 可以处理托管非托管 
    * 只有 True 时处理托管
  * 最佳策略
    * 两者都实现 
    * 防止忘记调用 Dispose
    * Dispose 必须调用 GC.SuppressFinalize(this)
* 释放内存由 GC 负责
* GC 模式
  * 工作站
  * 服务器
  * config
* 弱引用
  * WeakReference 
* 泛型集合替代非泛型集合
  *  装箱拆箱
  *  装箱会带来大量垃圾回收(线程堆栈 -> 托管堆)
  *  安全检查
* String 比较
  * == 调用 Equals, EqualsHelper 类复杂 
  * 使用 Compare
  * .length == 0 
* using
  * 实现 IDisposable 均可使用
  * 无论失败与否都会调用 Dispose 
  * try{} finally{Dispose}*
* 延迟对象实例化
  * Lazy

