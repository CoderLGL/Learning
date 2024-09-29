# SwiftUI 中 MVVM 的完整指南（转）



> 原文：[SwiftUI 中 MVVM 的完整指南 [附示例]](https://www.swiftanytime.com/blog/mvvm-in-swiftui)



## 1. 什么是 MVVM？

**MVVM 代表“模型-视图-视图模型”**，是一种用于分离应用程序不同方面的架构模式。它由 Microsoft 架构师 Ken Cooper 和 Ted Peters 于 2005 年推出，旨在明确区分 UI、数据（模型）和连接两者的逻辑（视图模型）。

这种分离使得代码更易于维护和测试。以下是每个部分的功能分解：

- **模型：**这代表了应用程序的数据和核心功能。它包含业务逻辑，但不直接与用户界面交互。
- **视图：**这是用户与之交互的用户界面 (UI)。视图本身对底层数据一无所知。它显示由 ViewModel 准备的数据，并将用户操作发送给 ViewModel。
- **ViewModel：**这是模型和视图之间的中介。它以视图能够理解和轻松使用的方式准备来自模型的数据。它还处理从视图收到的用户操作并相应地更新模型。

现在，想象一下你正在建造一栋房子。你不会把所有的砖块、木头和家具都堆放在一起，对吧？你需要一个地基（数据）、墙壁和屋顶（用户界面），以及一个建筑师（视图模型）来把所有东西组合在一起。MVVM 对应用程序的工作方式类似。



![MVVM](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/swiftUI_mvvm.png)



## 2. MVVM 中的数据流

用户与视图交互。这种交互可以是任何内容，例如单击按钮或在文本字段中输入一些文本，甚至可以是网络调用请求。视图将此用户交互传递给 viewModel，viewModel 使用所有必要的更改更新模型。

一旦模型更新，模型就会通知视图模型发生了变化。由于视图和视图模型是强绑定的，因此视图模型中的任何变化都会反映在视图中。

![MVVM Data](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/swiftUI_mvvm_%20data.jpg)

在 SwiftUI 中，ViewModel 通常是一个符合`ObservableObject`协议的类，允许它将更改发布到 View。View 使用`@StateObject`或`@ObservedObject`包装器来观察 ViewModel 并相应地更新 UI。



## 3. MVVM 有什么好处？

使用 MVVM 有几个优点。首先，它分离了应用程序的关注点。开发人员可以专注于构建数据逻辑（模型），而不必担心 UI，而 UI 设计人员可以专注于用户体验（视图），而无需深入了解数据结构。这使得代码更易于维护和测试。

假设你想改变应用中按钮的外观。使用 MVVM，你只需修改视图，而不会影响数据或核心功能。

MVVM 还促进了代码的简洁。业务逻辑在模型中保持独立，而 UI 逻辑由视图模型处理。这使得代码更易读，开发团队中的每个人都能更轻松地理解。

这就是 MVVM 背后的基本思想。现在让我们看看如何在一个简单的项目中实现 MVVM。



## 4. SwiftUI 中的 MVVM 示例：构建待办事项应用程序

让我们构建一个待办事项列表应用程序，该应用程序具有显示项目、标记项目完成/未完成以及使用 MVVM 添加新项目等功能。这是初学者代码。

**Model**

```swift
struct TodoItem: Identifiable {
    let id = UUID()
    var title: String
    var isChecked: Bool
}
```

该`TodoItem`结构包含一个唯一标识符、任务的标题和一个布尔标志以将其标记为已完成。

**View**

```swift
struct TodoListView: View {
    @State private var items: [TodoItem] = [
        TodoItem(title: "Grocery shopping", isChecked: true),
        TodoItem(title: "Finish report for client A", isChecked: false),
        TodoItem(title: "Learn a new coding language", isChecked: true),
        TodoItem(title: " Take out the trash", isChecked: false),
    ]

    var body: some View {
        NavigationView {
            List {
                ForEach(items) { item in
                    TodoItemView(item: item) {
                        // Update items array
                        if let index = items.firstIndex(where: { $0.id == item.id }) {
                            items[index].isChecked.toggle()
                        }
                    }
                }
            }
            .navigationTitle("To-Do List")
            .toolbar {
                Button(action: {
                    // Add new item
                    items.append(TodoItem(title: "New Item", isChecked: false))
                }) {
                    Label("Add", systemImage: "plus")
                }
            }
        }
    }
}

struct TodoItemView: View {
    var item: TodoItem
    let onToggle: () -> Void

    var body: some View {
        HStack {
            Text(item.title)
                .foregroundColor(item.isChecked ? .gray : .black)
            Spacer()
            Image(systemName: item.isChecked ? "checkmark.circle" : "circle")
                .onTapGesture {
                    onToggle()
                }
        }
    }
}
```

这里我们有一个`items`数组，它是一个列表`TodoItem`。`TodoItemView`显示项目的标题和基于其 isChecked 状态的复选框图标。此外，我们在工具栏中有一个按钮，用于将新项目添加到`items`数组中。



![](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/swiftUI_mvvm_TodoList_1.gif)

在这种方法中，直接`TodoListView`管理`items`数组。用户交互（例如切换完成或添加新项目）涉及在视图本身内更新数组。这可能会导致诸如紧密耦合、难以阅读的代码和难以测试等问题。虽然这种方法适用于像这样的简单应用，但 MVVM 为更大或更复杂的应用提供了更清晰的关注点分离和更好的可维护性。

现在让我们看看如何在代码中实现 MVVM。实现 MVVM 简单来说就是将关注点从数据管理（视图模型）和表示（视图）中分离出来。

## 实现 MVVM

### 1. 创建一个ViewModel：

首先，我们需要创建一个`TodoViewModel`类来管理数据和逻辑。

```swift
class TodoViewModel {

}
```

现在我们需要将所有业务逻辑转移到此处`TodoViewModel`。

```swift
class TodoViewModel {
    var items: [TodoItem] = [
        TodoItem(title: "Grocery shopping", isChecked: true), 
        TodoItem(title: "Finish report for client A", isChecked: false), 
        TodoItem(title: "Learn a new coding language", isChecked: true), 
        TodoItem(title: " Take out the trash", isChecked: false)]

    func toggleItem(item: TodoItem) {
        if let index = items.firstIndex(where: { $0.id == item.id }) {
            items[index].isChecked.toggle()
        }
    }

    func addItem() {
        items.append(TodoItem(title: "New Item", isChecked: false))
    }
}
```

包含`TodoViewModel`一个数组`TodoItem`并提供添加新项目和切换其完成状态的功能。

### 2. 更新 TodoListView：

现在我们需要更新`TodoListView`以使用`TodoViewModel`而不是管理数据本身。为此，删除`@State`属性`items`并将其替换为 的实例`TodoViewModel`。虽然`@State`主要用于`Int`、`String`、等值类型`Bool`，但`@StateObject`它是为引用类型（类）设计的。

```swift
@StateObject private var viewModel = TodoViewModel()
```

当您在视图内创建`TodoViewModel`实例时，SwiftUI 会确保同一个实例在该视图及其子视图的整个生命周期内持续存在。`@StateObject`

现在从 viewModel 访问项目和功能。

```swift
struct TodoListView: View {
    @StateObject private var viewModel = TodoViewModel()

    var body: some View {
        NavigationView {
            List {
                ForEach(viewModel.items) { item in
                    TodoItemView(title: item.title) {
                        // Update items array
                        viewModel.toggleItem(item: item)
                    }
                }
            }
            .navigationTitle("To-Do List")
            .toolbar {
                Button(action: {
                    // Add new item
                    viewModel.addItem()
                }) {
                    Label("Add", systemImage: "plus")
                }
            }
        }
    }
}
```

现在你可能会收到如下错误：

> 通用结构“StateObject”要求“TodoViewModel”符合“ObservableObject”

我们需要让我们的 viewModel 确认`ObservableObject`。`ObservableObject`用于在 MVVM 架构中启用 viewModel 和视图之间的双向数据绑定。

```swift
class TodoViewModel: ObservableObject { ... }
```

但是您可能已经注意到，即使`items`  viewModel 中的数组正在更新，您的视图也没有更新。这是因为，之前当`items`它被定义为`@State`属性时，当值发生变化时它会触发 UI 更新。但现在，我们需要明确告诉 UI 观察 viewModel 内部所需的更改。为此，我们可以使用`@Published`属性包装器。

```swift
@Published var items: [TodoItem] = [
    TodoItem(title: "Grocery shopping", isChecked: true),
    TodoItem(title: "Finish report for client A", isChecked: false),
    TodoItem(title: "Learn a new coding language", isChecked: true),
    TodoItem(title: " Take out the trash", isChecked: false),
]
```

`@Published`当视图模型中标有 的属性（如本例中的项目）被修改时，会`ObservableObject`自动发出通知。然后视图会获取此通知，从而触发刷新并更新 UI 以反映数据的变化。视图无需手动跟踪视图模型中的更改。它只需绑定到已发布的属性，然后 UI 更新就会自动处理。

现在代码的工作方式与使用 MVVM 之前完全相同。

### 3. 更新 TodoItemView（可选）：

由于数据和操作现在由视图模型处理，您可以`TodoItemView`通过删除 onToggle 参数并仅依赖 viewModel 来进一步改进。由于我们`@StateObject`在父视图中使用，所有子视图现在都可以使用名为 的属性包装器访问共享视图模型`@ObservedObject`。`@ObservedObject`在子视图中用于访问和响应由`ObservableObject`使用 从父视图创建和共享的实例管理的数据`@StateObject`。它对于将视图连接到`ObservableObject`管理与视图功能相关的数据的任何现有实例也很有用。

```swift
struct TodoItemView: View {
    var item: TodoItem
    @ObservedObject var viewModel: TodoViewModel

    var body: some View {
        HStack {
            Text(item.title)
                .foregroundColor(item.isChecked ? .gray : .black)
            Spacer()
            Image(systemName: item.isChecked ? "checkmark.circle" : "circle")
                .onTapGesture {
                    viewModel.toggleItem(item: item)
                }
        }
    }
}
```

该`onTapGesture`操作现在正在调用`toggleItem`viewModel 中的方法。viewModel`TodoItemView`作为参数传递。

```swift
TodoItemView(item: item, viewModel: viewModel)
```

这也与以前一样。

现在想象一下我们需要将复选框分离到这样的新视图中。

```swift
struct CheckBox: View {
    @ObservedObject var viewModel: TodoViewModel
    let item: TodoItem
    var body: some View {
        Image(systemName: item.isChecked ? "checkmark.circle" : "circle")
            .onTapGesture {
                viewModel.toggleItem(item: item)
            }
    }
}
```

所以我们的`TodoItemView`意志现在看起来像这样。

```swift
struct TodoItemView: View {
    @ObservedObject var viewModel: TodoViewModel
    let item: TodoItem

    var body: some View {
        HStack {
            Text(item.title)
                .foregroundColor(item.isChecked ? .gray : .black)
            Spacer()
            CheckBox(viewModel: viewModel, item: item)
        }
    }
}
```

在这里，您可以看到，即使此视图中从未使用过 viewModel，也会将其传递给`TodoItemView`as 。它只是将其传递给其子视图之一（ ）。与通常将子视图连接到其直接父视图层次结构中的共享对象的 不同，SwiftUI 有另一个名为 的属性包装器，它允许视图从视图层次结构中任何位置建立的实例访问数据，甚至是高于几个级别。`@ObservedObject``CheckBox``@ObservedObject``@EnvironmentObject``ObservableObject`

您想要共享的实例`ObservableObject`不会直接沿视图层次结构传递下去。相反，它会被注入到环境中。

```swift
.environmentObject(viewModel)
```

这个环境本质上是一个全局上下文，可供明确请求它的子视图访问。

视图可以`ObservableObject`使用访问共享`@EnvironmentObject`。

```swift
@EnvironmentObject var viewModel: TodoViewModel
```

与 类似`@ObservedObject`，它在视图中声明一个属性。然后，SwiftUI 在视图层次结构中向上搜索以`ObservableObject`在环境中找到兼容的实例。

这是我们视图的最终代码。

```swift
struct TodoListView: View {
    @StateObject private var viewModel = TodoViewModel()

    var body: some View {
        NavigationView {
            List {
                ForEach(viewModel.items) { item in
                    TodoItemView(item: item)
                }
            }
            .navigationTitle("To-Do List")
            .toolbar {
                Button(action: {
                    // Add new item
                    viewModel.addItem()
                }) {
                    Label("Add", systemImage: "plus")
                }
            }
            .environmentObject(viewModel)
        }
    }
}

struct TodoItemView: View {    
    let item: TodoItem
    var body: some View {
        HStack {
            Text(item.title)
                .foregroundColor(item.isChecked ? .gray : .black)
            Spacer()
            CheckBox(item: item)
        }
    }
}

struct CheckBox: View {
    @EnvironmentObject var viewModel: TodoViewModel
    let item: TodoItem
    var body: some View {
        Image(systemName: item.isChecked ? "checkmark.circle" : "circle")
            .onTapGesture {
                viewModel.toggleItem(item: item)
            }
    }
}
```

注意`.environmentObject(viewModel)`在 NavigationView 中如何使用。这会将`viewModel`实例沿视图层次结构向下广播，使其可供嵌套视图访问，例如`CheckBox`。

![](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/swiftUI_mvvm_TodoList_2.gif)

**[点击此处下载完整项目](https://github.com/SwiftAnytime/MVVMBootcamp)**

**关于 MVVM 的要点：**

- 管理`TodoViewModel`数据（`items`）和逻辑（添加和切换项目）。
- 定义`TodoListView`UI 结构并绑定到视图模型的数据。
- `TodoItemView`并呈现`CheckBox`数据并与视图模型交互以执行操作。



# 使用 MVVM 进行依赖注入



依赖注入 (DI) 是一种向视图提供其运行所需对象的方法，而不是让视图自己创建它们。通过注入依赖项，您可以在单元测试期间提供模拟对象，从而让您隔离视图的行为。

假设我们的 To-Do 应用针对每个任务都有一个“详细信息”屏幕。在这种情况下，我们可能需要一个新的 viewModel，以作为`item`输入并针对该项目执行必要的操作。

下面介绍了如何使用依赖注入为每个项目创建详细信息屏幕：

1. **定义 DetailViewModel：**

```swift
class DetailViewModel: ObservableObject {
    @Published var item: TodoItem
    @Published var title: String

    init(item: TodoItem) {
        self.item = item
        title = item.title
    }

    func toggleItem(item: TodoItem) {
        self.item.isChecked.toggle()
    }

    func updateItem() {
        // Update logic for the item based on title and isChecked properties
    }
}
```

它`DetailViewModel`管理特定 的详细信息`TodoItem`。它在初始化期间将项目作为输入，并将其属性公开为`@Published`双向数据绑定。

2. **创建 DetailView：**

```swift
struct DetailView: View {
    @StateObject var viewModel: DetailViewModel

    var body: some View {
        VStack {
            HStack {
                TextField("Title", text: $viewModel.title)
                    .foregroundColor(viewModel.item.isChecked ? .gray : .black)
                Spacer()
                Image(systemName: viewModel.item.isChecked ? "checkmark.circle" : "circle")
                    .onTapGesture {
                        viewModel.toggleItem(item: viewModel.item)
                    }
            }
            Button("Save") {
                viewModel.updateItem()
            }
        }
        .padding()
        .navigationTitle("Details")
    }
}
```

显示`DetailView`项目详细信息并允许编辑。它使用`@StateObject`属性包装器绑定到 DetailViewModel。单击“保存”会触发`updateItem`视图模型中的函数，该函数可以更新底层数据源（此处未显示）。

3. **注入依赖**

现在让我们通过将传递给初始化程序并使用它来创建来将依赖项（`TodoItem`）注入到中。`DetailView` `TodoItem` `DetailViewModel`

```swift
extension DetailView {
    init(item: TodoItem) {
        _viewModel = StateObject(wrappedValue: DetailViewModel(item: item))
    }
}

struct DetailView: View {
    @StateObject var viewModel: DetailViewModel

    var body: some View {
        VStack {
            HStack {
                TextField("Title", text: $viewModel.title)
                    .foregroundColor(viewModel.item.isChecked ? .gray : .black)
                Spacer()
                Image(systemName: viewModel.item.isChecked ? "checkmark.circle" : "circle")
                    .onTapGesture {
                        viewModel.toggleItem(item: viewModel.item)
                    }
            }
            Button("Save") {
                viewModel.updateItem()
            }
        }
        .padding()
        .navigationTitle("Details")
    }
}
```

的扩展`DetailView `定义了一个新的初始化程序，该初始化程序以`TodoItem`作为输入。此初始化程序负责创建依赖项，即`DetailViewModel`实例。`DetailView`不需要知道如何创建`DetailViewModel`本身；它只需要它提供的功能。

现在我们可以`DetailView`在视图中使用了`TodoListView`。

```swift
struct TodoListView: View {
    @StateObject private var viewModel = TodoViewModel()

    var body: some View {
        NavigationView {
            List {
                ForEach(viewModel.items) { item in
                    NavigationLink(destination: DetailView(item: item)) {
                        TodoItemView(item: item)
                    }
                }
            }
            .navigationTitle("To-Do List")
            .toolbar { ... }
            .environmentObject(viewModel)
        }
    }
}
```

单击`TodoItemView`现在导航到`DetailView`，在初始化期间将当前项传递给`DetailViewModel`。这有效地注入了依赖项。

![](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/swiftUI_mvvm_TodoList_3.gif)

你可能已经注意到，该`toggleItem()`函数在两种情况下都存在`TodoViewModel`，`DetailViewModel`尽管其实现在两种情况下有所不同。这时你可能需要协议。

通过组合这些部分，您将获得一个简单的待办事项列表应用，该应用展示了 MVVM 中的关注点分离。View 仅显示数据并处理用户交互，而 ViewModel 则管理底层数据和逻辑。这种分离使代码更简洁、更易于维护且更易于测试。

[点击此处下载完整项目](https://github.com/SwiftAnytime/MVVMBootcamp)

这是实现 MVVM 的基本示例。你可以通过以下方式进一步增强它：

- 在视图模型中添加错误处理。
- 使用 ViewModel 进行路由和导航。
- 将网络调用分离到单独的服务层。
- 单独添加视图模型和视图的单元测试。
- 使用 UserDefaults、CoreData 或数据库实现数据持久性。
- 实现依赖注入：如何将 ViewModel 与具体数据源分离。

## 文件夹结构

现在让我们看看如何建立文件夹结构。

![](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/swiftUI_mvvm_TodoList_4.jpg)

![](https://github.com/CoderLGL/Learning/raw/main/iOS/SwiftUI/Resources/swiftUI_mvvm_TodoList_5.jpg)

此结构将与应用程序特定功能相关的代码分组到文件夹中。每个功能文件夹可能包含模型、视图和 ViewModel 的子文件夹。每个功能可能有多个模型、视图和 ViewModel。

 

## MVVM 是 SwiftUI 中的反模式吗？

MVVM 和 SwiftUI 完美地互补，因为它们都注重状态管理。MVVM 专注于模型中的单一事实来源，这与 SwiftUI 基于状态的声明式 UI 相一致。ViewModel 和 View 之间数据流的可预测性以及两种方法的模块化简化了 UI 更新的推理并增强了可测试性。

然而，苹果开发者社区内部有一个强烈的论点，即 MVVM 对于 SwiftUI 来说是不必要的。相反，应用程序应该主要通过视图直接与数据模型交互来构建。对于数据和逻辑有限的非常简单的应用程序，MVVM 可能会带来不必要的复杂性。关注点分离可能不那么有价值，而 ViewModel 的样板代码可能会抵消其好处。此外，SwiftUI 的内置功能（如绑定）在某些情况下可能会使 ViewModel 变得多余。如果实施不当，MVVM 可能会导致更复杂的数据流，并可能造成混乱。

总体而言，MVVM 是一种用于构建可维护且灵活的 UI 的强大架构，但它并不是一刀切的解决方案。最终，是否将 MVVM 与 SwiftUI 结合使用取决于项目的具体需求。请考虑应用的复杂性、团队的经验以及简单性和可维护性之间的权衡。
