# SwiftUI 中的关键属性包装器：@State、@Binding、@StateObject、@ObservedObject、@EnvironmentObject 和 @Environment



# 1. @State

`@State` 是 SwiftUI 中最基础的状态管理机制。它用于声明一个视图的私有状态，并且在状态变化时自动刷新视图。

- **用法**: 适用于视图的局部状态，该状态不会被其他视图直接访问或修改。
- **特性**:
  - `@State` 变量是由视图自己管理的，它的生命周期与视图绑定。
  - 当 `@State` 变量改变时，视图会自动重新渲染。
  - 只能在视图内部使用，不能直接传递给其他视图。
- **注意点**:
  - `@State` 适合处理轻量级的、局部的状态，不适合跨视图层次结构共享的状态。

```swift
// Button 在点击时会刷新数据，会使Text显示的内容改变。
struct CounterView: View {
    @State private var count = 0

    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") {
                count += 1
            }
        }
    }
}
```



# 2. @Binding

`@Binding` 用于在父视图和子视图之间共享状态。它允许子视图访问和修改父视图中的状态。

- **用法**: 当需要将一个状态传递给子视图，并允许子视图修改该状态时使用。
- **特性**:
  - `@Binding` 并不创建新的状态，而是引用了另一个状态（通常是 `@State`）。
  - 通过 `@Binding`，子视图可以双向绑定父视图的状态。
- **注意点**:
  - 使用 `@Binding` 时，需要确保绑定的状态在某个父视图中存在且被管理。
  - `@Binding` 不能脱离它引用的状态独立存在。

```
```





















**来源资料：**
[AsiaSun.](https://blog.csdn.net/IOSSHAN/article/details/141212949?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522F236430E-0308-4CE4-BF1A-B16058B7EDC6%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=F236430E-0308-4CE4-BF1A-B16058B7EDC6&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-141212949-null-null.142^v100^control&utm_term=%40StateObject&spm=1018.2226.3001.4187)

[东坡肘子](https://fatbobman.com/zh/posts/exploring-key-property-wrappers-in-swiftui)

