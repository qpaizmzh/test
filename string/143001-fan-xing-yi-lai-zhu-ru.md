# 泛型依赖注入

![](/assets/spring-14-1.png)

* 在图中，比如说userService是UserRepository中的一个成员变量，正常情况下是要在UserRepository类中添加自动注入的注解来维护关系的
* 现在可以通过BaseService&lt;T&gt;和BaseRepository&lt;T&gt;来直接维护这种关系，不需要再在每一个子类中各自维护这种关系，只需要在BaseRepository&lt;T&gt;的父类中添加BaseService&lt;T&gt;的成员变量，Spring就会自动根据泛型寻找对应的子类并把实例注入：

```
public class BaseRepository<T> {

	@Autowired
	private BaseService<T> service;
	
	public void addNew(T entity){
		System.out.println("addNew by " + dao);
		service.save(entity);
	}
	
}

//后面创建子类的时候根据各自的父类创建，泛型一致的就会把父类有的这种关系的另一个子类注入进去
```



