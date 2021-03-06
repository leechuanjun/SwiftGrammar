# 基本使用

## GCD 异步

* 模拟在后台线程加载数据

```swift
func loadData() {
    DispatchQueue.global().async { () -> Void in
        print("耗时操作 \(NSThread .currentThread())")
    })
}
```

* 尾随闭包，如果闭包是最后一个参数，可以用以下写法
* 注意上下两段代码，`}` 的位置

```swift
func loadData() {
    DispatchQueue.global().async { () -> Void in
        print("耗时操作 \(NSThread .currentThread())")
    }
}
```

* 闭包的简写，如果闭包中没有参数和返回值，可以省略

```swift
func loadData() {
    DispatchQueue.global().async {
        print("耗时操作 \(NSThread .currentThread())")
    }
}
```

### 自定义闭包参数，实现主线程回调

* 添加没有参数，没有返回值的闭包

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    loadData {
        print("完成回调")
    }
}

// MARK: - 自定义闭包参数
func loadData(finished: ()->()) {

    DispatchQueue.global().async {
        print("耗时操作 \(NSThread.currentThread())")

        DispatchQueue.main.async(execute: {
            print("主线程回调 \(NSThread.currentThread())")

            // 执行回调
            finished()
        }
    }
}
```

* 添加回调参数
* 嵌套的 GCD Xcode 不会改成尾随闭包

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    loadData4 { (html) -> () in
        print(html)
    }
}

/// 加载数据
/// 完成回调 - 传入回调闭包，接收异步执行的结果
func loadData4(finished: @escaping (_ html: String) -> ()) {

    DispatchQueue.global().async {
        print("加载数据 \(NSThread.currentThread())")

        DispatchQueue.main.async(execute: {
            print("完成回调 \(NSThread.currentThread())")

            finished(html: "<h1>hello world</h1>")
        }
    }
}
```



