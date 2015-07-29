title: Protostuff
date: 2015-07-28 15:21:09
tags: Java基础
---

# Protostuff

标签（空格分隔）： Java基础-序列化

---

参考文档
> http://dongliu.net/post/587142

Google的protobuf是一个优秀的序列化工具，跨语言、快速、序列化后体积小。

protobuf的一个缺点是需要数据结构的预编译过程，首先要编写.proto格式的配置文件，再通过protobuf提供的工具生成各种语言响应的代码。由于java具有反射和动态代码生成的能力，这个预编译过程不是必须的，可以在代码执行时来实现。有个[protostuff][1]已经实现了这个功能。

protostuff基于Google protobuf，但是提供了更多的功能和更简易的用法。其中，protostuff-runtime实现了无需预编译对java bean进行protobuf序列化/反序列化的能力。

```java
Schema schema = RuntimeSchema.getSchema(Foo.class);
LinkedBuffer buffer = getApplicationBuffer();

//serialize
try{
    byte[] protostuff = ProtostuffIOUtil.toByteArray(foo, schema, buffer);
}finally{
    buffer.clear();
}

//deserialize
Foo f = new Foo();
ProtostuffIOUtil.mergaFrom(protostuff, f, schema);
```

protostuff-runtime的局限是序列化前需预先传入schema，反序列化不负责对象的创建只负责复制，因而必须提供默认构造函数。此外，protostuff还可以按照protobuf的配置序列化生成json/yaml/xml等格式。

完整的工具类如下：
```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

import org.objenesis.Objenesis;
import org.objenesis.ObjenesisStd;

import com.dyuproject.protostuff.LinkedBuffer;
import com.dyuproject.protostuff.ProtostuffIOUtil;
import com.dyuproject.protostuff.Schema;
import com.dyuproject.protostuff.runtime.RuntimeSchema;
import com.lxz.rpc.sample.client.Person;


/*
 * 序列化工具类（基于Protostuff实现）
 */
public class SerializationUtil {
	private static Map<Class<?>, Schema<?>> cachedSchema = new ConcurrentHashMap<>();
	private static Objenesis objenesis = new ObjenesisStd(true);
	
	private SerializationUtil(){
	}
	
	private static<T> Schema<T> getSchema(Class<T> cls){
		Schema<T> schema = (Schema<T>) cachedSchema.get(cls);
		if(schema == null){
			schema = RuntimeSchema.createFrom(cls);
			if(schema != null){
				cachedSchema.put(cls, schema);
			}
		}
		return schema;
	}
	
	/*
	 * 序列化（对象->字节数组）
	 * @param 传入需要序列化的对象
	 * @return 返回给序列化后的字节数组
	 */
	public static<T> byte[] serialize(T obj){
		Class<T> cls = (Class<T>) obj.getClass();
		LinkedBuffer buffer = LinkedBuffer.allocate(LinkedBuffer.DEFAULT_BUFFER_SIZE);
		try{
			Schema<T> schema = getSchema(cls);
			return ProtostuffIOUtil.toByteArray(obj, schema, buffer);
		}catch(Exception e){
			throw new IllegalStateException(e.getMessage(), e);
		}finally{
			buffer.clear();
		}
	}
	
	/*
	 * 反序列化（字节数组->对象）
	 * @param data 传入序列化后的数组
	 * @param cls 传入需要反序列化的类
	 * @return 返回序列化后的对象
	 */
	public static <T> T deserialize(byte[] data, Class<T> cls){
		try{
			T message = (T) objenesis.newInstance(cls);
			Schema<T> schema = getSchema(cls);
			ProtostuffIOUtil.mergeFrom(data, message, schema);
			return message;
		}catch(Exception e){
			throw new IllegalStateException(e.getMessage(), e);
		}
	}
	
	public static void main(String[] args){
		Person beforeSerPerson = new Person("Alexa", "Cloudera");//创建对象
		byte[] tmp = SerializationUtil.serialize(beforeSerPerson);//对其进行序列化
		Person afterSerPerson = (Person)SerializationUtil.deserialize(tmp, Person.class);//对其反序列化
		System.out.println(afterSerPerson.getFirstName() + " " + afterSerPerson.getLastName());
	}
}
```


  [1]: http://code.google.com/p/protostuff/