### 代码分析

**声明**

```Java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

ArrayList基于数组实现，支持快速访问。RandomAccess支持快速访问。

数组默认大小为10

```Java
private static final int DEFAULT_CAPACITY = 10;
```



**2. 扩容**

