# 明细控制器

## 知识点

* `IBOutlet` 和 `IBAction` 连线都会连到同一个 swift 文件 中，因此在连线时一定注意选择类型

## 布局

* 新建 `TLDetailTableViewController` 继承自 `UITableViewController`
* 在 `Main.storyboard` 中给 `TLDetailTableViewController` 嵌入导航控制器
* 新增 `UITableViewController` 并指定类型为 `TLDetailTableViewController`
* 从 `Cell` 连线到 `TLDetailTableViewController` 并选择 `Show`
* 新增 `Navigation Item`，并且设置标题
* 新增 `Bar Button Item` 并且设置标题为 `保存`，`Enabled` 设置为 `false`
* 新增 `姓名` & `年龄` 文本框

## 连线

* 连接 `IBOutlet`

```swift
@IBOutlet weak var nameText: UITextField!
@IBOutlet weak var ageText: UITextField!
```

* 连线 `IBAction`，将两个文本框的 `Editing Changed` 事件连线到 `textDidChanged` 函数

```swift
/// 文本变化
@IBAction func textChange(_ sender: Any) {
    navigationItem.rightBarButtonItem?.enabled = nameText.hasText() && ageText.hasText()
}
```

* 连线保存按钮

```swift
/// 保存
@IBAction func savePerson(_ sender: Any) {
}
```

## 传递数值

* 定义模型属性，准备接收数值

```swift
/// 个人模型
var person: TLPerson?
```

* 在 `TLListTableViewController` 中实现 `prepare` 函数，传递参数

```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    // 判断目标控制器
    guard let vc = segue.destination as? TLDetailTableViewController else {
        return
    }
    
    // 取出当前选中行
    guard let indexPath = tableView.indexPathForSelectedRow else {
        return
    }
    
    // 传递数值
    vc.person = persons![indexPath.row]
}
```

* 在 `viewDidLoad` 中设置控件内容

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    nameText.text = person?.name
    ageText.text = String(person?.age ?? 0)
}
```

* 抽取函数，设置导航按钮的激活状态

```swift
private func setupNavigation() {
    navigationItem.rightBarButtonItem?.enabled = nameText.hasText() && ageText.hasText()
}
```

* 分别修改 `viewDidLoad` 和 `textChange` 的函数调用
* 实现 `savePerson` 函数

```swift
/// 保存
@IBAction func save(sender: AnyObject) {
    person?.name = nameText.text
    person?.age = Int(ageText.text!) ?? 0
    
    print(person)
}
```

## 闭包回调

* 定义闭包回调属性

```swift
/// 保存个人信息回调
var savePersonCallBack: (()->())?
```

* 完成回调

```swift
/// 保存
@IBAction func savePerson(sender: AnyObject) {
    person?.name = nameText.text
    person?.age = Int(ageText.text!) ?? 0
    
    print(person)
    
    // 完成回调
    savePersonCallBack?()
    
    // 控制器弹栈
    navigationController?.popViewControllerAnimated(true)
}
```

* 设置完成回调

```swift
override func prepare(segue: UIStoryboardSegue, sender: AnyObject?) {
    // 判断目标控制器
    guard let vc = segue.destinationViewController as? DetailViewController else {
        return
    }
    
    // 取出当前选中行
    guard let indexPath = tableView.indexPathForSelectedRow else {
        return
    }
    
    // 传递数值
    vc.person = persons![indexPath.row]
    // 完成回调
    vc.savePersonCallBack = {
        self.tableView.reloadData()
    }
}
```

* 简化完成回调

```swift
vc.savePersonCallBack = self.tableView.reloadData
```
