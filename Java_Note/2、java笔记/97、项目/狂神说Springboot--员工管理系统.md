数据库模拟

```
@Repository
public class DepartmentDao {
    //模拟数据库数据
    private static Map<Integer, Department> departments = null;

    static{
        departments=new HashMap<Integer, Department>(); //创建一个部／／衣
        departments.put(101,new Department(101,"教学部"));
        departments.put(102,new Department(102,"市场部"));
        departments.put(103,new Department(103,"教研部"));
        departments.put(104,new Department(104,"运营部"));
        departments.put(105,new Department(105,"后勤部"));
    }
    public Collection<Department> getDepartments(){
        return departments.values();
    }
    public Department getDepartmentById(Integer id){
        return departments.get(id);
    }
}
```





首页配置

页面国际化

登陆+拦截器

员工列表展示

​	提取公共页面

​	列表循环展示