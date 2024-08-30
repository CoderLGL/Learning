# Learning



# View

在 **SwiftUI** 中，这是一个视图协议，任何自定义的视图都遵循该协议，并实现协议属性 **body** 来提供具体的视图内容和行为。


```Swift
public protocol View {

    associatedtype Body : View
    
    // @ViewBuilder 修饰的 body 属性返回一个遵循 View 协议的视图，
    // 并且在主线程(@MainActor)上计算或访问它。这个属性可以包含多个视图的组合，最终用于构建用户界面。
    
    @ViewBuilder @MainActor var body: Self.Body { get }

}
```

整个**View**实体一般由： **View**，**Modifier**,**Layout**组成。




# Animation

**转场动画** 和 **变形动画**

## 转场动画

- **不同的View之间的变化**  
A <-> B  
有 <-> 无

## 变形动画

- **同一个View在变化**    
  视图的形状、大小、位置或其他属性的变化。

  

# View的布局

当多个视图的布局优先级相同时，SwiftUI 会首先尝试满足每个视图的固有内容大小。如果容器中的空间不足，具有相同优先级的视图将可能会被压缩或截断。

可以用`layoutPriority`提高优先级，优先级默认为`layoutPriority(0)`。



# shapeStyle

`ShapeStyle` 是一个协议，主要用于定义形状的样式。能够自定义形状的填充颜色、渐变、图案等样式。

1. `Color` 是最基本的样式类型，用于将形状填充为单一颜色。
2. `LinearGradient` 用于创建线性渐变效果，即颜色从一个点渐变到另一个点。
3. `RadialGradient` 创建径向渐变效果，即颜色从中心向外渐变。
4. `AngularGradient` 创建角度渐变效果，即颜色在一个圆环内逐渐过渡。
5. `Image` 可以作为填充样式使用，但通常用于将图像作为填充的样式，例如图案填充。
6. `Gradient` 是渐变的基础数据结构，通常与 `LinearGradient`、`RadialGradient` 和 `AngularGradient` 一起使用。它定义了颜色的过渡效果。



# State 、Binding、 Environment

## State
- 用来存储和观察一个Value Type的变化。
- Thread-Safe。
- State不该被用任何方式传递：`设为private,避免由外部修改`

## Binding

