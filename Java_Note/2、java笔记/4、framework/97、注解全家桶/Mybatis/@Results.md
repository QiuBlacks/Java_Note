## 相当于ResultMap标签

```java
@CacheNamespace(blocking = true)
public interface IUserDao {

    @Select("select * from user")
    
    //标注id方便其他方法使用
    @Results(id="userMap",value={
            //id为主键标识，固只有一个属性
            @Result(id=true,column = "id",property = "userId"), 
            @Result(column = "username",property = "userName"),
            @Result(column = "address",property = "userAddress"),
            @Result(column = "sex",property = "userSex"),
            @Result(column = "birthday",property = "userBirthday"),
            @Result(property = "accounts",column = "id",
                    many = @Many(select = "com.itheima.dao.IAccountDao.findAccountByUid",
                                fetchType = FetchType.LAZY))
    })
    List<User> findAll();

    /**
     * 根据id查询用户
     * @param userId
     * @return
     */
    @Select("select * from user  where id=#{id} ")
    // @ResultMap(vaule={"userMap"})
    @ResultMap("userMap")
    User findById(Integer userId);

    /**
     * 根据用户名称模糊查询
     * @param username
     * @return
     */
    @Select("select * from user where username like #{username} ")
    @ResultMap("userMap")
    List<User> findUserByName(String username);

}
```

