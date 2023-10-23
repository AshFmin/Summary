# JAVA

## JNDI









### 类使用

#### ThreadLocal

 

#### ThreadContext

利用ThreadLocal封装的HashMap 

避免多线程访问

封装方法:

```java
// ThreadContext: HashMap
// 利用ThreadLocal来实现线程隔离
// ThreadLocal 设置为static
// 多个线程访问一个 static ThreadLocal 实例 -> 各自线程ThreadLocalMap -> 获得各自线程的ThreadContext实例
public class ThreadContext{
    private static ThreadLocal<ThreadContext> ctx = new ThreadLocal<ThreadContext>(){
        // override initialValue 方法
        // 该方法在调用ThreadLocal.get时 
        // 若ThreadLocal没有为当前线程初始化ThreadLocalMap 或 ThreadLocalMap中不包含改ThreadLocal对象
        // 则会调用SetInitialValue -> initialValue方法 将ThreadLocalMap中设置为<this, initialValue>
        protected synchronized ThreadContext initialValue(){
            return new ThreadContext();
        }
    }
    private Map<String,Object> map = new HashMap();
    
    public static ThreadContext getContext(){
        return (ThreadContetx)ctx.get();
    }
    
    public void setAttribute(String key, Object Value){
        this.map.put(key, value);
    }
    
    public Object getAttribute(String key){
        return this.map.get(key);
    }
}
```

