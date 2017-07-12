# 懒加载

> 在 iOS 开发中，懒加载是无处不在的

* 懒加载的格式如下：

```swift
lazy var person: Person = {
    print("懒加载")
    return Person()
}()
```

* 懒加载本质上是一个闭包
* 完整写法如下：供参考
    {} 包装代码
    () 执行代码
     
    日常开发：
    1. 闭包中的智能提示不好
    2. 闭包中如果出现 self. 还需要注意循环引用
     
* 以上代码可以改写为以下格式

```swift
let personFunc = { () -> Person in
    print("懒加载")
    return Person()
}
lazy var demoPerson: Person = self.personFunc()
```

* 懒加载的简单写法

```swift
lazy var demoPerson: Person = Person()
```
