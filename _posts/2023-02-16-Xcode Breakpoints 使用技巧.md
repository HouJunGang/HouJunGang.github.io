---
title: Xcode Breakpoints 使用技巧
excerpt_separator: <!--more-->
description: 
categories:
 - Xcode技巧
tags: Xcode, Breakpoints
---

## 全局断点

在Xcode左侧的导航条中点击断点栏目，点击左下角的`+`号即可添加全局断点

![全局断点](https://github.com/HouJunGang/HouJunGang.github.io/blob/main/ArticlePictures/2023-02-16-Xcode%20Breakpoints%20%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/1.png)

|全局断点类型|作用|
|-|-|
|Swift Error Breakpoint|定位到throw、try抛出的异常处|
|Exception Breakpoint|定位到崩溃处，例如数组越界、非空参数置为nil等|
|Symbolic Breakpoint|定位到Symbol栏任意输入的方法函数处，例如viewDidLoad等|
|Runtime issue Breakpoint|运行时问题在Xcode中以紫色小三角形展示，可以用此断点定位到相应的运行时问题处|
|Constraint Error Breakpoint|当自动布局的控件出现约束错误时，定位到约束出错处|
|Test Failure Breakpoint|当出现测试没通过的情形时，定位到在失败的测试处|

## 普通断点

在代码左侧行数位置单击即可添加一个普通断点，当程序运行到此的时候会自动暂停。这种断点可以满足大部分时候的调试需求，但是有事会有些复杂一点的调试需求，例如如下代码

```swift
import UIKit

class ViewController: UIViewController {

    lazy var dataSource: [Int] = {
        var array: [Int] = []
        for number in 0..<100 {
            array.append(number)
        }
        return array
    }()

    var tableView: UITableView = UITableView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        tableView.register(UITableViewCell.self, forCellReuseIdentifier: "UITableViewCell")
        tableView.delegate = self
        tableView.dataSource = self
        tableView.frame = view.frame
        view.addSubview(tableView)
    }

}

extension ViewController: UITableViewDelegate, UITableViewDataSource {
    func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        return 100
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return dataSource.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "UITableViewCell") ?? UITableViewCell()
        cell.textLabel?.text = "\(dataSource[indexPath.row])"
        return cell
    }
}
```

上面的代码简单展示了 `100` 个 `cell` 分别显示 `0~99` 的数字
如果要对上述的代码中的 `cellForRowAt` 方法进行断点调试，并且要求定位到第 `3` 个 `cell` 处，普通断点会在每一次执行此方法时暂停程序，而我们需要单击两次 `Continue` 按钮，此时虽然稍显麻烦但还可以接受，但如若要求定位到第 `27` 个 `cell` 时，就过于麻烦了，此时就需要编辑此断点来增加断点的功能了

## 编辑普通断点

双击断点或者右键单击断点点击 `Edit Breakpoint`

![编辑断点](https://github.com/HouJunGang/HouJunGang.github.io/ArticlePictures/2023-02-16-Xcode-Breakpoints使用技巧/2.png)

|编辑项|作用|
|-|-|
|Name|断点名称|
|Condition|在此栏输入条件表达式，为 `true` 才暂停程序|
|Ignore|忽略次数，例如输入 `2` 时，程序第一次和第二次执行到断点位置时不会暂停|
|Action|满足条件时执行的行为通过点击 `add action` 来添加，可以添加多个 `action`|
|Options|启用此选项后，断点不会暂停执行|

上述的要求可以简单的在 `Ignore` 一栏输入 `26` 来实现，也可以在 `Condition` 中输入 
```swift
indexPath.row == 26
```

接下来我们可能还希望在断点的时候执行一些操作，例如打印点信息，这可以通过添加 `Action` 来完成

![添加Action](https://github.com/HouJunGang/HouJunGang.github.io/ArticlePictures/2023-02-16-Xcode-Breakpoints使用技巧/3.png)

![Action类型](https://github.com/HouJunGang/HouJunGang.github.io/ArticlePictures/2023-02-16-Xcode-Breakpoints使用技巧/4.png)

|Action类型|作用|
|-|-|
|AppleScript|通过AppleScript语法指定要执行的操作|
|Capture GPU Workload|捕获GPU工作负载|
|Debugger Command|可以在调试控制台执行LLDB指令，例如 `po`|
|Log Message|自定义在控制台打印日志|
|Shell Command|加载指定路径下的shell脚本，执行特定指令|
|Sound|断点时播放声音|

上述代码中，如果要打印每个 `cell` 的信息，我们平常会使用 `print`

```swift
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "UITableViewCell") ?? UITableViewCell()
        cell.textLabel?.text = "\(dataSource[indexPath.row])"
        print("\(indexPath.row): \(cell)")
        return cell
    }
```

这样的打印也可以在断点中完成，所需要打印的变量需要用两个 `@` 包起来，`%B` 可以打印此断点的名称，`%H` 可以打印这个断点触发的次数，如果要让此断点不暂停程序，记得勾上 `Options` 选项

![cell信息断点](https://github.com/HouJunGang/HouJunGang.github.io/ArticlePictures/2023-02-16-Xcode-Breakpoints使用技巧/5.png)

在程序中我们可能有大量的调试打印，使用断点来打印信息不会在代码中嵌入许多 `print` 方法，后期在管理所有的日志打印的时候，也能通过左侧的断点栏目进行统一管理，但是要注意 **断点是 Xcode 管理的，而不是在代码中，所以更换电脑打开项目，所有的断点都会消失**
