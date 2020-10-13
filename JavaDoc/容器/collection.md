### 1、概述

Collection存储对象的集合。

#### 1.1 Set

- TreeSet：基于红黑树，支持有序操作。

- HashSet：基于哈希表实现，支持快速查找。

- LinkedHashSet：具有HashSet的查找效率，内部使用双向链表维护元素插入顺序



#### 1.2 List

- ArrayList：基于动态数组实现，支持随机访问。
- Vector：与ArrayList类似，线程安全。
- LinkedList：基于双向链表实现，只能顺序访问，可以快速插入和删除元素。



#### 1.3 Queue

- LinkedList：双向链表
- PriorityQueue：优先队列