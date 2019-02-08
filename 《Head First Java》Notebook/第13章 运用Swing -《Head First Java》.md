## Swing组件

组件(component)是比widget更为正确的术语。它们就是会放在GUI上面的东西。在Swing中，所有的组件都继承自`javax.swing.JComponent`。

## 布局管理器
>布局的情境
>1. 制作面板并添加3个**按钮**。
>2. **面板**的布局管理器会询问每个**组件**理想的大小应该是多少。
>3. 面板的布局管理器以它的布局策略来决定是否应该要尊重全部或部分的按钮理想。
>4. 把面板加到**框架**上。
>5. **框架**的布局管理器询问**面板**的理想尺寸。
>6. 框架的布局管理器以它的布局策略来决定是否应该要尊重全部或部分的面板理想。

### BorderLayout
在BorderLayout中，位于`north`和`south`的组件先占据位置，这里的**组件**会得到预设的高度，然后位于`east`和`west`的**组件**会得到预设的宽度，但得到的高度要扣除`north`和`south`的高度，**位于`center`只能捡剩下的**。

### FlowLayout
`JPanel`的布局管理器的默认布局是FlowLayout布局。

## 操作Swing组件
关于`JTextField`,`JTextArea`,`JScrollPane`,`JCheckBox`,`JList`的用法从书413页看起。