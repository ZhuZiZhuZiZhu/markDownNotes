# 1. 关于IO的一些小问题的总结和思考

1. 为什么输入输出流需要不能复用同一个流？

   主要原因是因为如果输入流和输出流共用一个流会导致数据的混淆和错误。另外，输入流可能需要对数据进行解析和处理，而输出流可能需要对数据进行格式化和编码。同时他们出现异常的时候也会不一样，输入流的异常一般是读到末尾了，而输出流则是写入文件失败。

   这就牵扯到一个小问题，就是流是不是只能单向。答案当然是不是，可以通过使用缓冲区从而实现双向流。

2. 为什么channel可以是全双工的并且必须和buffer同时使用？

   其实两个问题说的是一个事。如果channel想要实现双工（也就是可以输入也可以输出）就必须依赖buffer。通过翻转状态来实现读或者写的功能。其实这也是为什么流虽然不依赖buffer但是仍然不能做到双工的原因。

   但是有一个问题我还没有想明白，channel既然是全双工的，但是在同一时间一个buffer只能是读或者写的状态，为什么还能称之为全双工呢？

3. 明白了前面两个问题，难免会想到，如果我是用stream加buffer是否就能实现channel的功能呢？

   答案是否定的，因为流是顺序读写的数据流，无法像channel一样跳跃读写。说是这么说，那为什么双向流依然不可以像channel一样跳跃读写。其实就是因为channel在缓冲区中加入了末尾标记和目前写到哪里的标记，这样可以判断写活着读是否合法。至少我是这么理解的。那如果流先读到缓冲区，然后跳着读写行不行，我觉得没啥问题。。。

4. 为什么NIO使用直接内存会快？

   原理并不复杂，其实就是可以节省一次copy的消耗，我们都知道用户态不能直接访问IO设备，需要经过切换内核态，IO存储到内核态缓冲区，复制到用户态缓冲区，切换回用户态的过程。而如果JVM想要进行IO则需要从JVM的堆中先拷贝到用户态的缓冲区中，因为GC会导致堆内存不稳定，为了保证复制地址的内容不会因为GC的算法导致移动就必须暂停GC，所以JVM选择多拷贝一次来代替暂停GC。而navtive memory则可以减少一次拷贝。

5. 

# 2. JAVA NIO

```java
// 打开文件
File file = new File("file-map-sample.txt");
//删除文件
file.delete();
//创建新文件
//这个操作会创建一个空白的文件
file.createNewFile();

//获取channel
RandomAccessFile randomAccessFile = new RandomAccessFile(file,"rw");
FileChannel fileChannel = randomAccessFile.getChannel();

MappedByteBuffer mappedByteBuffer = fileChannel.map(FileChannel.MapMode.READ_WRITE,0,Integer.MAX_VALUE);

System.out.println("MappedByteBuffer capacity " + mappedByteBuffer.capacity());

long currentTime = System.currentTimeMillis();
int size = Integer.MAX_VALUE / 4;
for (int i = 0; i < size; i++) {
    mappedByteBuffer.putInt(i);
}
mappedByteBuffer.force();
fileChannel.close();
randomAccessFile.close();
System.out.println("MappedByteBuffer Write " + (System.currentTimeMillis() - currentTime) + " ms");


```

而在这之前，我们需要弄明白一些事情。当我们 new 一个流对象时究竟发生了什么？与之密切相关的 FileDescriptor 又是什么？它与 Channel 之间有着怎么样的联系？
