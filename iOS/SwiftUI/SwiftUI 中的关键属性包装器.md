# SwiftUI 中的关键属性包装器：@State、@Binding、@StateObject、@ObservedObject、@EnvironmentObject 和 @Environment

![](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/SwiftUI%20State.png)



在 SwiftUI 中，属性包装器（Property Wrappers）用于管理视图的数据流和状态。每个包装器有其独特的用途，以下是 SwiftUI 中常用的关键属性包装器：

### 1. `@State`

`@State` 用于声明视图的局部状态。该状态由 SwiftUI 管理，当状态发生变化时，视图会重新渲染。通常用于在当前视图内部存储简单的数据值，如布尔值、数字或字符串。

- **用途**：在视图内部存储和修改状态。
- **作用范围**：仅限于视图自身，不能在其他视图中共享。
- **使用场景**：当视图需要独立管理其自身的局部状态（如按钮开关、输入框内容）时，使用 `@State`。

**示例**：

```swift
struct CounterView: View {
    @State private var count = 0

    var body: some View {
        Button("Increment") {
            count += 1
        }
        Text("Count: \(count)")
    }
}
```

### 2. `@Binding`

`@Binding` 用于在父视图和子视图之间共享和双向绑定状态。子视图通过 `@Binding` 访问和修改父视图的状态，而不创建自己的状态副本。

- **用途**：在视图之间共享状态，允许双向数据绑定。
- **作用范围**：通常用于子视图，从父视图传递绑定的值。
- **使用场景**：当子视图需要修改父视图的状态，但不持有状态时，使用 `@Binding`。

**示例**：

```swift
struct ParentView: View {
    @State private var isOn = false

    var body: some View {
        ChildView(isOn: $isOn)
    }
}

struct ChildView: View {
    @Binding var isOn: Bool

    var body: some View {
        Toggle(isOn: $isOn) {
            Text("Toggle")
        }
    }
}
```

### 3. `@StateObject`

`@StateObject` 用于管理符合 `ObservableObject` 协议的类对象。`@StateObject` 确保 SwiftUI 在视图生命周期中保持对该对象的强引用，适用于视图首次创建并持有的对象。

- **用途**：用于创建和持有符合 `ObservableObject` 协议的对象，适合视图中需要创建并保持模型对象的状态。
- **作用范围**：视图生命周期内，创建并持有对象。
- **使用场景**：当视图需要管理一个对象的完整生命周期并监控其变化时，使用 `@StateObject`。

**示例**：

```swift
class UserViewModel: ObservableObject {
    @Published var name = "John Doe"
}

struct UserView: View {
    @StateObject private var viewModel = UserViewModel()

    var body: some View {
        Text("User: \(viewModel.name)")
    }
}
```

### 4. `@ObservedObject`

`@ObservedObject` 用于引用外部传入的 `ObservableObject` 对象，视图会观察对象的变化并更新。与 `@StateObject` 不同的是，`@ObservedObject` 不持有对象，只是监视它。

- **用途**：用于监控外部传入的 `ObservableObject` 对象的变化。
- **作用范围**：不持有对象，只是观察和响应变化。
- **使用场景**：当视图需要监控从外部传入的数据模型对象的变化时，使用 `@ObservedObject`。

**示例**：

```swift
class TaskViewModel: ObservableObject {
    @Published var tasks = ["Task 1", "Task 2"]
}

struct ParentView: View {
    @StateObject var taskViewModel = TaskViewModel()

    var body: some View {
        ChildView(taskViewModel: taskViewModel)
    }
}

struct ChildView: View {
    @ObservedObject var taskViewModel: TaskViewModel

    var body: some View {
        List(taskViewModel.tasks, id: \.self) { task in
            Text(task)
        }
    }
}
```

### 5. `@EnvironmentObject`

`@EnvironmentObject` 是一种全局的共享数据方式，允许跨多个视图层次结构传递 `ObservableObject`，并自动更新所有依赖此对象的视图。与 `@ObservedObject` 相似，但它不需要通过每个视图的构造函数传递，而是通过环境直接注入。

- **用途**：在视图层次结构中全局共享 `ObservableObject`，通常用于全局状态。
- **作用范围**：可以在多个视图中共享。
- **使用场景**：当需要在多个视图中共享全局数据模型时，使用 `@EnvironmentObject`。

**示例**：

```swift
class Settings: ObservableObject {
    @Published var isDarkMode = false
}

struct ParentView: View {
    @StateObject var settings = Settings()

    var body: some View {
        ChildView()
            .environmentObject(settings)
    }
}

struct ChildView: View {
    @EnvironmentObject var settings: Settings

    var body: some View {
        Toggle(isOn: $settings.isDarkMode) {
            Text("Dark Mode")
        }
    }
}
```

### 6. `@Environment`

`@Environment` 用于读取环境中的值，如当前语言（`Locale`）、颜色方案（`ColorScheme`）等。SwiftUI 提供了许多预定义的环境值，这些值可以直接注入到视图中。

- **用途**：读取系统或应用的环境值，如设备方向、颜色模式等。
- **作用范围**：可用于视图中读取系统或全局的环境值。
- **使用场景**：当需要访问全局环境值，如当前系统的颜色模式或字体设置时，使用 `@Environment`。

**示例**：

```swift
struct ContentView: View {
    @Environment(\.colorScheme) var colorScheme // 读取环境中的颜色模式

    var body: some View {
        Text("Hello, World!")
            .foregroundColor(colorScheme == .dark ? .white : .black)
    }
}
```

### 总结：

| 属性包装器           | 用途                                   | 典型使用场景                                             |
| -------------------- | -------------------------------------- | -------------------------------------------------------- |
| `@State`             | 视图内部的局部状态管理                 | 视图内部需要独立管理的数据，如计数器、输入框             |
| `@Binding`           | 父子视图之间的状态共享和双向绑定       | 子视图需要修改父视图的状态，如表单输入或切换按钮         |
| `@StateObject`       | 创建并持有 `ObservableObject` 对象     | 视图需要管理数据模型的完整生命周期，如用户信息或数据列表 |
| `@ObservedObject`    | 观察外部传入的 `ObservableObject` 对象 | 子视图需要监控父视图传递的对象的变化，如任务列表         |
| `@EnvironmentObject` | 全局数据共享                           | 多个视图共享的全局设置，如应用主题或用户配置             |
| `@Environment`       | 读取系统环境值                         | 需要动态读取系统或应用的环境设置，如颜色模式或语言配置   |

掌握这些属性包装器的用途和使用场景，能够帮助你在构建 SwiftUI 应用时高效管理视图的状态和数据流。

























