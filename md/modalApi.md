### API

#### Modal.props

调用

参数 | 说明 | 类型 | 默认值
:---- | :----- | :----- | :-----
prefixCls | 设置样式前缀 | string | mt

#### Modal.method

Modal 对外提供的方法

方法名 | 说明 | 参数 | 返回值
:---- | :----- | :----- | :-----
show(content) | 显示模态框 | content: ReactClass | Promise: 在模态框内容组件调用 onClose 方法之后被 resolve，可以设置在模态框被关闭后处理事务
