## 简述

```java
@AndroidAopModifyExtendsClass(
    value = "修改目标类",
    isParent = false // value 是指向类的类名还是类的继承类，默认false
    excludeWeaving = 排除织入的范围
    includeWeaving = 包括织入的范围
)
```

- `isParent = true` 就是修改所有继承类是 value 的类
- `isParent = false` 就是修改类名是 value 的类
- excludeWeaving 和 includeWeaving 类似于 [入门处的配置](/AndroidAOP/zh/getting_started/#app-buildgradle-androidaopconfig) 的 exclude 和 include

这个功能比较简单，修改类的继承类，```value``` 位置填写要修改的类的全名，被注解的类就是修改后的继承类。

另外填写类名如果是内部类时不使用`$`字符，而是用`.`



!!! note
    - **:warning::warning::warning:但需要特别注意的是修改后的继承类不可以继承被修改的类，修改后的类的继承类一般都设置为修改前的类的继承类**
    - **:warning::warning::warning:原来的继承类如果是带有泛型信息的，请注意修改后的继承类也需要是具有一样泛型信息**
    - **当你修改这个切面的配置后多数情况下你应该clean项目再继续开发**

## 使用示例

### 例一

- 如下例所示，就是要把 ```AppCompatImageView``` 的继承类替换成 ```ReplaceImageView1```

- 因为设置的 `isParent = false` （不写默认false）,所以只会替换 ```AppCompatImageView``` 的继承类

```java
@AndroidAopModifyExtendsClass(
        value = "androidx.appcompat.widget.AppCompatImageView",
        isParent = false
)
public class ReplaceImageView1 extends ImageView {
    public ReplaceImageView1(@NonNull Context context) {
        super(context);
    }
    public ReplaceImageView1(@NonNull Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public ReplaceImageView1(@NonNull Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    public void setImageDrawable(@Nullable Drawable drawable) {
        super.setImageDrawable(drawable);
        //做一些监测或者再次修改
    }
}
```

!!! note
    上述 `ReplaceImageView1` 的继承类不可以是 `AppCompatImageView`,这样改完之后就变成了 `AppCompatImageView` --> `ReplaceImageView1` --> `AppCompatImageView`

### 例二

- 如下例所示，就是要把 所有父类是 ```AppCompatImageView``` 的类的继承替换成 ```ReplaceImageView2```

- 因为设置的 `isParent = true`,所以继承自 ```AppCompatImageView``` 的类，可能不止一个，他们的继承类都会被替换

```java
@AndroidAopModifyExtendsClass( 
        value = "androidx.appcompat.widget.AppCompatImageView",
        isParent = true
)
public class ReplaceImageView2 extends AppCompatImageView {
    public ReplaceImageView2(@NonNull Context context) {
        super(context);
    }
    public ReplaceImageView2(@NonNull Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public ReplaceImageView2(@NonNull Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    public void setImageDrawable(@Nullable Drawable drawable) {
        super.setImageDrawable(drawable);
        //做一些监测或者再次修改
    }
}
```

!!! note
    上述 `ReplaceImageView2` 的继承类可以是 `AppCompatImageView`,这样改完之后 原本的 `A` -->  `AppCompatImageView` 就变成了 `A` --> `ReplaceImageView2` --> `AppCompatImageView`


## 使用启示

1、在类的继承关系中修改继承类可以在中间重写一些方法，如此一来可以在中间处理一下原有逻辑，也是对对象的某些方法被调用的监测

