# ​二、匹配器

这里是将注解的属性作为匹配器，只要配置了这个属性，那么就相当于启用了这个匹配器，匹配器可以有多个，只要匹配上任何一个，那么就算匹配上。注解 `@FieldWhiteMatcher` 或者`@FieldBlackMatcher`中的属性进行用法介绍。

```java
/**
 * 属性白名单匹配器
 * 修饰基本的属性（Boolean Byte Character Short Integer Long Double Float）、String和java.util.Date类型，属性的所有可用的值
 *
 * @author zhouzhenyong
 * @since 2019/3/7 下午9:47
 */
@Repeatable(FieldWhiteMatchers.class)
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface FieldWhiteMatcher {

    /**
     * 针对不同场景下所需的匹配模式的不同，默认"_default_"，详见{@link MkConstant#DEFAULT_GROUP}
     * @return 分组
     */
    String[] group() default {MkConstant.DEFAULT_GROUP};

    /**
     * 可用的值， 如果允许值为null，那么添加一个排除的值为"null"，因为不允许直接设置为null
     * @return 只允许的值的列表
     */
    String[] value() default {};

    /**
     * 可用的值对应的类型
     * @return 对应的枚举类型
     */
    FieldType type() default FieldType.DEFAULT;

    /**
     * 枚举类型的判断
     *
     * 注意：该类型只用于修饰属性的值为String类型或者Integer类型的属性，String为枚举的Names，Integer是枚举的下标
     *
     * @return 该属性为枚举对应的类，否则不生效
     */
    Class<? extends Enum>[] enumType() default {};

    /**
     * 数据范围的判断
     *
     * @return
     * 如果是数值类型，且位于范围之内，则核查成功，当前支持的核查功能：[a,b]，[a,b)，(a,b]，(a,b)，(null,b]，(null,b)，[a, null), (a, null)
     * 如果是时间类型，则也可以进行比较，比如["2019-08-03 12:00:32.222", "2019-08-03 15:00:32.222")，也可以用单独的一个函数
     * past: 表示过去
     * now: 表示现在
     * future: 表示未来
     * 同时也支持范围中包含函数（其中范围内部暂时不支持past和future，因为这两个函数没有具体的时间），比如：
     * past 跟(nul, now)表示的相同
     * future 跟(now, null)表示的相同
     * 支持具体的范围，比如：("2019-08-03 12:00:32", now)，其中对应的时间类型，目前支持这么几种格式
     * yyyy
     * yyyy-MM
     * yyyy-MM-dd
     * yyyy-MM-dd HH
     * yyyy-MM-dd HH:mm
     * yyyy-MM-dd HH:mm:ss
     * yyyy-MM-dd HH:mm:ss.SSS
     */
    String range() default "";

    /**
     * 数据条件的判断
     *
     * 根据Java的运算符构造出来对应的条件表达式来进行判断，而其中的数据不仅可以和相关的数据做条件判断，还可和当前修饰的类的其他数据进行判断，
     * 其中当前类用#root表示，比如举例如下，对象中的一个属性小于另外一个属性，比如：{@code #current + #root.ratioB + #root.ratioC == 100}
     * 其中#current表示当前的属性，#root表示当前的属性所在的对象，ratioB为该对象的另外一个属性，如上只有在属性ratioA是大于ratioB的时候核查才会拦截
     *
     * @return 用于数据字段之间的条件表达式（即条件结果为true还是false），当前条件支持Java的所有运算符，以及java的所有运算结果为boolean的表达式
     * 算术运算符：{@code  "+"、"-"、"*"、"/"、"％"、"++"、"--"}
     * 关系运算符：{@code "=="、"!="、">"、"<"、">="、"<="}
     * 位运算符：{@code "＆"、"|"、"^"、"~"、"<<"、">>"、">>>"}
     * 逻辑运算符：{@code "&&"、"||"、"!"}
     * 赋值运算符：{@code "="、"+="、"-="、"*="、"/="、"(%)="、"<<="、">>="、"&="、"^="、"|="}
     * 其他运算符：{@code 条件运算符（?:）、instanceof运算符}
     * {@code java.lang.math}中的所有函数，比如：{@code min,max,asb,cell}等等
     */
    String condition() default "";

    /**
     * 可用的值对应的正则表达式
     * @return 对应的正则表达式
     */
    String regex() default "";

    /**
     * 系统自己编码判断
     *
     * @return 调用的核查的类和函数对应的表达式，比如："com.xxx.AEntity#isValid"，其中#后面是方法，方法返回boolean或者包装类，第一个入参为当前Field对应的类型或者子类，第二个入参为属性对应的对象
     */
    String judge() default "";

    /**
     * 是否启用核查
     * @return true：禁用核查，false：启用核查
     */
    boolean disable() default false;
}
```
 