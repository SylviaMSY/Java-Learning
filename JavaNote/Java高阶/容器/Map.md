## HashMap

1. **底层结构：**

   - JDK1.7：数组+链表
   - JDK1.8：数组+链表+红黑树

2. **容器大小：**即数组的大小，默认16

3. **容器原理：**

   - hashCode()方法（位运算），计算插入数组位置

     ``` java
     
     ```

   - 哈希冲突时，使用链表解决，头插法

   - 容量超过0.75时，自动扩容

4. **常用参数：**

   ``` java
   DEFAULT_INITIAL_CAPACITY = 1 << 4 //Hash表默认初始容量
   MAXIMUM_CAPACITY = 1 << 30	//最大Hash表容量
   DEFAULT_LOAD_FACTOR = 0.75f	//默认加载因子
   TREEIFY_THRESHOLD = 8	//链表转红黑树阈值
   UNTREEIFY_THRESHOLD = 6	//红黑树转链表阈值
   MIN_TREEIFY_CAPACITY = 64	//链表转红黑树时hash表最小容量阈值，达不到优先扩容。
   ```

5. **拓容机制：JDK 1.7**

   **多线程下容易形成俩链表环，造成死锁**

   - 阀值：初始大小的0.75倍（牛顿二项式，基于空间与时间折中），元素容量超过阀值时，自动拓容

   - 拓容量：2倍（因为要满足2的次幂，所以每次时2倍拓容）

     ``` java
      void transfer(Entry[] newTable, boolean rehash) {
          int newCapacity = newTable.length;
          for (Entry<K,V> e : table) {
              while(null != e) {
                  Entry<K,V> next = e.next;
                  if (rehash) {//重新分配，导致链表环的关键
                      e.hash = null == e.key ? 0 : hash(e.key);
                  }
                  int i = indexFor(e.hash, newCapacity);
                  e.next = newTable[i];
                  newTable[i] = e;
                  e = next;
              }
          }
      }
     ```

6. **拓容机制：JDK 1.8**

   - 引入红黑树，容量超过64时链表转红黑树，否则优先拓容

   - 链表过长，阀值设置TREEIFY_THRESHOLD = 8（不是链表长度），链表长度>8，链表9的时候转红黑树。

   - **基于高低位拓容，避免链表环，死锁问题。**

     ``` java
     Node<K,V> loHead = null, loTail = null;
     Node<K,V> hiHead = null, hiTail = null;
     Node<K,V> next;
         do {
             next = e.next;
             if ((e.hash & oldCap) == 0) {
                 //yangguo.hashcode & 16 = 0，用低位指针
                 if (loTail == null)
                     loHead = e;
                 else
                     loTail.next = e;
                 loTail = e;
             }
             else {
                  //yangguo.hashcode & 16 》 0 高位指针
                 if (hiTail == null)
                     hiHead = e;
                 else
                     hiTail.next = e;
                 hiTail = e;
             }
         } while ((e = next) != null);
     if (loTail != null) {
         loTail.next = null; 
         newTab[j] = loHead;，移到新的数组上的同样的index位置
     }
     if (hiTail != null) {
         hiTail.next = null;
         newTab[j + oldCap] = hiHead; //index 3+16 = 19
     }
     完全绕开rehash，要满足高低位移动，必须数组容量是2的幂次方
     
         
     分库分表，在线扩容
         2台master tuling（1,2,3,4） -> 4台
     ```

     

## ConcurrentHashMap

1. **底层结构：加入同步锁机制（仅put操作）**

   - **JDK 1.7：Segment(extends ReentrantLock)里存放一个table数组，锁粒度较大**

     <img src="C:\Users\MSY\AppData\Roaming\Typora\typora-user-images\image-20210923103734342.png" alt="image-20210923103734342" style="zoom: 50%;" />

   - **JDK 1.8：先CAS分配table地址，再synchronized对数组元素加锁，粒度小（单个地址内的元素、链表）**

     <img src="C:\Users\MSY\AppData\Roaming\Typora\typora-user-images\image-20210923103809481.png" alt="image-20210923103809481" style="zoom: 50%;" />

2. **拓容机制：老数据的迁移并发执行，效率更高**
3. 成员变量



## CopyOnWrite机制

