http://www.51testing.com/html/95/115295-804841.html  把这个网址的总结下来。可以做一次分享



https://blog.csdn.net/u010663871/article/details/73603460



https://blog.csdn.net/softwave/article/details/6238747



## JVM的优化

1、一般来说，当survivor区不够大或者占用量达到50%，就会把一些对象放到老年区。通过设置合理的eden区，survivor区及使用率，可以将年轻对象保存在年轻代，从而避免full GC，使用-Xmn设置年轻代的大小

2、对于占用内存比较多的大对象，一般会选择在老年代分配内存。如果在年轻代给大对象分配内存，年轻代内存不够了，就要在eden区移动大量对象到老年代，然后这些移动的对象可能很快消亡，因此导致full GC。通过设置参数：-XX:PetenureSizeThreshold=1000000，单位为B，标明对象大小超过1M时，在老年代(tenured)分配内存空间。

3、一般情况下，年轻对象放在eden区，当第一次GC后，如果对象还存活，放到survivor区，此后，每GC一次，年龄增加1，当对象的年龄达到阈值，就被放到tenured老年区。这个阈值可以同构-XX:MaxTenuringThreshold设置。如果想让对象留在年轻代，可以设置比较大的阈值。

4、设置最小堆和最大堆： -Xmx  和  -Xms  稳定的堆大小堆垃圾回收是有利的，获得一个稳定的堆大小的方法是设置-Xms和-Xmx的值一样，即最大堆和最小堆一样，如果这样子设置，系统在运行时堆大小理论上是恒定的，稳定的堆空间可以减少GC次数，因此，很多服务端都会将这两个参数设置为一样的数值。稳定的堆大小虽然减少GC次数，但是增加每次GC的时间，因为每次GC要把堆的大小维持在一个区间内。

5、一个不稳定的堆并非毫无用处。在系统不需要使用大内存的时候，压缩堆空间，使得GC每次应对一个较小的堆空间，加快单次GC次数。基于这种考虑，JVM提供两个参数，用于压缩和扩展堆空间。

（1）-XX:MinHeapFreeRatio 参数用于设置堆空间的最小空闲比率。默认值是40，当堆空间的空闲内存比率小于40，JVM便会扩展堆空间

（2）-XX:MaxHeapFreeRatio 参数用于设置堆空间的最大空闲比率。默认值是70， 当堆空间的空闲内存比率大于70，JVM便会压缩堆空间。

（3）当-Xmx和-Xmx相等时，上面两个参数无效

6、通过增大吞吐量提高系统性能，可以通过设置并行垃圾回收收集器。

​	（1）-XX:+UseParallelGC:年轻代使用并行垃圾回收收集器。这是一个关注吞吐量的收集器，可以尽可能的减少垃圾回收时间。

（2）-XX:+UseParallelOldGC:设置老年代使用并行垃圾回收收集器。

7、尝试使用大的内存分页：使用大的内存分页增加CPU的内存寻址能力，从而系统的性能。-XX:+LargePageSizeInBytes参数设置内存页的大小，

8、使用非占用的垃圾收集器。-XX:+UseConcMarkSweepGC老年代使用CMS收集器降低停顿。

9、-XXSurvivorRatio=3，表示survivor:eden = 2:3

10、JVM性能调优的工具：

（1）jps（Java Process Status）：输出JVM中运行的进程状态信息(现在一般使用jconsole)

（2）jstack：查看java进程内线程的堆栈信息。

（3）jmap：用于生成堆转存快照

（4）jhat：用于分析jmap生成的堆转存快照（一般不推荐使用，而是使用Ecplise Memory Analyzer）

（3）jstat是JVM统计监测工具。可以用来显示垃圾回收信息、类加载信息、新生代统计信息等。

（4）VisualVM：故障处理工具