**Java 动态代理，CGlib**
1. Java动态代理
    动态意味着运行时生成，不是静态编译期间生成。Java动态代理只能对实现接口的类进行。proxy 要关联一个  `InvocationHandler`。proxy 通过
`Proxy.newInstance(classloader, interfaces, invocationHandler)` 来生成
继承自Object的toString,hasCode,equals，和接口中定义的其它方法行为一致，其它方法者不走InvocationHandler，比如getClass,只返回代理的class

生成的proxy

Example:
```
public interface Hello {
 String sayHello(String toName);
}

public class HelloImp implements Hello {

    @Override
    public String sayHello(String toName) {
        String helloString = "Mehtod in HelloIMP: Hello" + toName;
        System.out.println(helloString);
        return helloString;
    }

}

public class LogInvocationhandler implements InvocationHandler {
    private Object object;
    public LogInvocationhandler(Object obj) {
        this.object = obj;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("This is dynamic proxy : " + proxy.getClass().getName());
        System.out.println("LogInvocationhandler invoke begin");
   
        System.out.println("method: "+ method.getName());
        if (args != null) {
            for(Object methodArg : args){ 
                System.out.println("arg: "+ methodArg); 
            }   
        }
     
        Object result = method.invoke(object, args);        
        System.out.println("LogInvocationhandler invoke end");
        return result;
    }

}


public class DynamicProxyTest {
    public static void main(String[] args) {
        Hello hello = new HelloImp();
        
        Hello helloProxy = (Hello)Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(), new Class[] {Hello.class}, new LogInvocationhandler(hello));
        String result = helloProxy.sayHello("zdj");
        
        System.out.println("proxy returned result is : " + result);
        //toString, hasCode, equals, same behavior with sayHello
        System.out.println(hello.toString());
        //it's will not go proxy
        System.out.println(helloProxy.getClass());
    }
}
```
End
