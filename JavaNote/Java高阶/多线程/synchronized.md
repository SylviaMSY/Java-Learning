





锁的粗化

锁的消除——逃逸分析

synchronized优化（1.6）：锁的膨胀升级（不可逆）



逃逸分析：如果对象只存在于当前栈中，就不会在堆内创建对象，而是直接在栈内分配内存创建对象