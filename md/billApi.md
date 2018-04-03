### API

#### Bill

bill的使用 推荐model配置可以配置的 外加mergeModel 挂载逻辑来完成

参数 | 说明 | 类型 | 默认值
:---- | :----- | :----- | :-----
prefixCls | 设置样式前缀 | string | mt
className | 设置类名 | string | -
mode | 设置Bill的状态 分为可编辑状态和查看状态,通过该属性集成了表单的编辑模式和查看模式 | string | default
size | 设置Bill中组件元素的大小，可选值有`default`和`small` | string | default
model | 设置表单的显示配置 下面会有一个demo的完整展示 | object | -
data | 设置表单的数据值，其中数据值支持完全打平的数据结构，后边示例会显示一个数据结构的示例 | object|-
onFieldChange | bill中任意字段发生改变时触发的事件，可以理解为bill的onChange事件 | function(code,value,bill)|-
parent | bill的父组件是bill的时候支持的传递，主要解决events中param自动取值的逻辑 <font color="#ff9801">该属性不推荐使用，后续版本会下掉</font> | object | -


<p><font color="#ff9801" size=1 >另外历史版本中还存在的`initModel`、`initData`和`notice` 暂时还支持，不建议使用<br/>
bill的model和data初始化赋值，通过参数的方式只支持一次，后续赋值通过setModel和setData来实现
</font></p>

bill的method，及bill本身可以对外提供的方法 

方法 | 说明 | 参数 | 返回值
:---- | :----- | :----- | :-----
setModel(model,callback) | 设置bill的model | model`object`:要更新的model <br/> callback`function`:更新完成model之后的回调方法 | -
setData(data,callback) | 设置bill的data | data`object`:需要更新的data <br/> callback`function`:数据更新完成之后执行的回调方法 | -
getData() | 获取bill当前的数据 | - | object  
setFieldModel(code,model) | 设置某个字段元素的配置 | code`string`:字段的code <br/> model`object`:该字段的model配置 | -
getFieldModel(code) | 根据code获取对应字段的配置 | code`string`:字段的code | object
setFieldsData(data,callback,notNowUpdate) | 设置某些字段的值，setData是完整赋值，setFieldsData是针对部分字段赋值 | data`object`:只包含要改变的数据 <br/> callback`function`:赋值生效之后的回调 <br/> notNowUpdate`boolean`:是否立即生效 | - 
getField(code) | 获取对应字段的react实例 | code`string`:字段的code | object  
validate() | 执行bill的校验逻辑 | - | boolean: `true` or `false`
clear() | 清空数据，清空掉bill上校验失败的提示等 | - | - 
reset() | 把数据还原到默认值状态，并把校验的提示清除掉| - | -
doControl(control) | 执行页面字段的控制联动控制 支持`show`(显示)、`hide`(不显示)、`disable`(禁用)、`editable`(可编辑)、`view`(查看模式)、`required`(必填控制)、`optional`(不必填控制)等字段状态的控制 | control`object`:{show:[],hide:[],...} | -

*另外历史版本中还存在的`doFeildValidate`暂时还支持，不建议使用*

#### Model

这里展示model的一个完整配置实例

```json
{
  "code":"bill",
  "idField":"id",
  "column":2,
  "alignment":"right",
  "forms":[
    {
      "code": "baseInfo",
      "label": "基本信息",
      "fields":[
        {
          "code":"id",
          "label":"ID",
          "type":"text",
          "show":false,
          "conf":{
            "disabled":true,
            "mode":"view",
          }
        },
        {
          "code":"name",
          "label":"用户名",
          "show":true,
          "type":"text",
          "conf":{
              "pattern":"^[0-9a-zA-Z_]{1,30}$",
              "required":true,
              "placeholder":"支持数字，大小写字母和下划线",
              "validation":"不能为空，支持数字、大小写字母和下划线，最大30字长"
          }
        },
        {
          "code":"nick",
          "label":"昵称",
          "type":"text",
          "show":true,
          "showHeader":true,
          "alignment":"right",
          "conf":{
            "trigger":"onBlur",
            "disabled":false,
            "mode":"default",
            "placeholder":"请输入8个字符以内的昵称",
            "maxLength": "8",
            "overLengthValidation": "最多只能输入8个字符",
            "validator":{
              "onBlur":[
                {"required":true,"whitespace":true,"message":"必填项必须输入"},
                {"pattern":"^[a-zA-Z1-9_]{1,8}$","message":"特殊字符不认识哦"}
              ],
              "onChange":[
                {"validator":true}
              ]
            }
          },
          "onFocus": (bill,value,e) => {
            console.log("这里去处理相关的逻辑");
          }
        },
        {
          "code":"address",
          "label":"地址",
          "type":"autoText",
          "conf":{
            "placeholder":"输入地址"
          }
        }
      ]
    }
  ],
  "events":[
    {
      "code":"address",
      "type":"bind",
      "params":["name"],
      "noClear": true,
      "url":"/service/dataset/address"
    },
    {
      "code":"address",
      "type":"fetch",
      "params":[],
      "fields": ["name"],
      "url":"/service/fetch/address"
    }
  ]
}
```
具体的字段配置描述如下

参数 | 说明 | 类型 | 默认值
:---- | :----- | :----- | :-----
code | bill的唯一标识 | string | -
idField | 设置表单数据的唯一主键，可以是一个不显示字段 | string | -
column | 设置表单每行显示的列数 | number | 2
forms | 分组的概念，一个表单可以分多个组，用多个forms来表示多个分组，每组可以有唯一表示、标题和字段集合| array | [ ]
events | 配置事件的地方，bill内置了`bind`、`fetch`和`control`三种类型的事件 | array |[ ] 

##### Model.form

model配置中form的参数

参数 | 说明 | 类型 | 默认值
:---- | :----- | :----- | :-----
code | 分组的唯一标识 | string | -
label | 分组的组描述，可以是字符串，也可以是一个自定义的JSX(react结构的dom) | string or jsx | -
hideIfNoFieldVisible | 是个开关，用于控制该分组没有字段要显示的时候是否显示这个区域的 | boolean | false
fields | 字段元素的配置集合 | array | -

##### <span id="fields">Model.form.field</span>

model中具体一个字段的配置

参数 | 说明 | 类型 | 默认值 |
:---- | :----- | :----- | :----- |
code | 字段的编码，在block中code具有较强的唯一性，与data的key直接对应 | string | -
label | 字段的名称，支持自定义为jsx | string or jsx | - 
type | 类型，内置支持的有 [text](/component/input)、 [number](/component/input) 、[password](/component/input) 、[textarea](/component/input)、 [time](/component/date-picker) 、[date](/component/date-picker) 、[rangeDate](/component/date-picker) 、[select](/component/select) 、[autoText](/component/select) 、[multiSelect](/component/select) 、[treeSelect](/component/tree-select) 、[treeMultiSelect](/component/tree-select) 、[radio](/component/radio) 、[checkbox](/component/checkbox) 、cunstom | string | text 
showCode | 控件显示字段的键, 只对 `select、treeSelect` 有效 | string | - 
Component | 自定义显示的控件, 只对 `custom` 类型的控件有效 | ReactClass | -
show | 是否可见，对不可见元素是不参与校验的 | boolean | true
full | 是否占满一整行，优先级高于bill的column | boolean | false 
alignment | 说明文字的对齐方式, 可选值为 `left right top` | string | right
showHeader | 是否显示说明文字 | boolean | true
addonBefore | 显示的前缀 | string or JSX | -
addonAfter | 显示的后缀 | string or JSX | -
idsOnlyToServer| 控制 getData 方法对该字段传出主键的数组, 对 `multiSelect/treeMultiSelect` 有效 | boolean | false
objToServer | 控制 getData 方法对该字段传出数据对象, 对 `select/treeSelect` 有效 | boolean | false
conf | field还提供一些高级配置，主要用来配置field自身的配置项 | object | - 
onChange | 当控件的值改变时的回调, 覆盖掉 Bill 监听字段改变时的所有处理，<br/> onFieldChange 是 Bill 接收的 onFieldChange 属性，<br/> doAction 指定走Bill内部处理数据变化的逻辑(字段值的改变,control/级联等事件的处理,调用onFieldChange) <br/>| function(value,bill,onFieldChange,doAction){}| -
onBind | 当 select/treeSelect/multiSelect/treeMultiSelect/autoText 中要去获取下拉数据的回调, 会覆盖掉 events 中给该字段声明的 bind 内置事件 | function(bill,code,filter,callback,errCallback) | -
onBindNode | 当 treeSelect/treeMultiSelect 中父节点要去获取子数据时的回调, 会覆盖掉 events 中给该字段声明的 bindNode 内置事件 | function(bill,code,filter,callback,errCallback) | -
onFetch | 当控件的值改变时的回调, 会覆盖掉 events 中给该字段声明的 control 内置事件 | function(bill,code,value,callback){} | -
onControl | 当控件的值改变时的回调, 会覆盖掉 events 中给该字段声明的 control 内置事件 | function(bill,code,value){} | -


对于不同的类型，支持的方法还有一些特殊的 

type | 方法 |
:---- | :----- | 
text、password、number、textarea | onFocus(bill, e)<br/>onBlur(bill, e)<br/>onClear(bill, e)<br/>onFetch(bill,code,value,callback)<br/>onControl(bill,code,value)
autoText、select、multiSelect | onFocus(bill, e)<br/>onBlur(bill, e)<br/>onClear(bill, e)<br/>onSelect(bill, option, e)<br/>onKeyDown(bill, e, activeItem)<br/>onBind(bill,code,filter,callback)<br/>onFetch(bill,code,value,callback)<br/>onControl(bill,code,value)
treeSelect、treeMultiSelect | onFocus(bill, e)<br/>onBlur(bill, e)<br/>onClear(bill, e)<br/>onSelect(bill, option, e)<br/>onKeyDown(bill, e, activeItem)<br/>onBind(bill,code,filter,callback)<br/>onBindNode(bill,code,node,callback)<br/>onFetch(bill,code,value,callback)<br/>onControl(bill,code,value)
upload | onBeforeUpload(bill,file)<br/>onBeforeUploadAll(bill,files)<br/>onError(bill,file,response)<br/>onSuccess(bill,file,response)<br/>onUpload(bill,code,file,uploadParams,headersConf)

#### Model.forms.field.conf

model 字段的自身配置

type | 说明 
:---- | :----- 
alltype(公共的) | prefixCls(string: 组件的样式前缀, 默认'mt'), <br/>defaultValue, 控件的默认值, 不同的控件支持的默认值类型不一样 <br/> emptyLabel(string: 组件无值的情况下只读时显示的文本, 默认''), <br/> domProps(obj: 原声组件的props') <br/><font color="#ff9801">可参考[示例](#conf-1)</font>
text、password、number、textarea | 'trigger', <br/>'clearIcon',<br/>'focusAfterClear',<br/>'showCount',<br/>'maxLength',<br/>'validator(bill,value)',<br/>'overLengthValidation',<br/>'cutIfOverLength',<br/>'domProps'<br/> <font color="#ff9801">可参考[示例](#conf-2)</font>
time | 'format', <br/>'disabledHours', <br/>'disabledMinutes', <br/>'disabledSeconds', <br/>'hideDisabledOptions', <br/><font color="#ff9801">可参考[date组件的api](/component/date-picker#api)</font>
date、rangeDate | 'format', <br/>'disableDate', <br/>'showUpToNow', <br/><font color="#ff9801">可参考[date组件的api](/component/date-picker#api)</font>
autoText | 'trigger', <br/>'clearIcon', <br/>'validator',<br/>'notFoundContent',<br/>'noSearchIfNoKeyword',<br/>'defaultActiveFirstOption',<br/>'delay',<br/><font color="#ff9801">可参考[select组件的api](/component/select#api)</font>
select、treeSelect | 'clearIcon', <br/>'notFoundContent',<br/>'noSearchIfNoKeyword',<br/>'defaultActiveFirstOption',<br/>'delay',<br/><font color="#ff9801">可参考[select组件的api](/component/select#api)和[treeSelect组件的api](/component/tree-select#api)</font>
multiSelect、treeMultiSelect | 'clearIcon',<br/>'notFoundContent',<br/>'noSearchIfNoKeyword',<br/>'defaultActiveFirstOption',<br/>'delay',<br/>'checkbox',<br/>'allCheck',<br/><font color="#ff9801">可参考[select组件的api](/component/select#api)和[treeSelect组件的api](/component/tree-select#api)</font>
checkbox | name', <br/>'showCheckall', <br/><font color="#ff9801">可参考[checkbox组件的api](/component/checkbox#api)</font>
radio | 'name', <br/><font color="#ff9801">可参考[radio组件的api](/component/radio#api)</font>
upload | 'locale',<br/>'multiple',<br/>'droppable',<br/>'fileType',<br/>'fileMaxSize',<br/>'uploadParams',<br/>'headersConf',<br/>'useFormData',<br/>'durationIfSucceed',<br/><font color="#ff9801">可参考upload组件的api(待补充)</font>


<span id="conf-1">conf-1示例：</span>

```json
{
  emptyLabel:"-",
  domProps:{...}
}
```
<span id="conf-2">conf-2示例：</span>

```json
{
  trigger:'onChange',
  clearIcon:false,
  focusAfterClear:true,
  showCount:3,
  maxLength:300,
  validator:function(bill,value){...},
  overLengthValidation:'最多只能输入300字',
  cutIfOverLength:false
}
```
**每个类型的高级属性可以参考各个组件的api**

#### Model.forms.field.defaultValue

type | 说明 | 示例
:---- | :----- | :----
text、password、number、textarea | string | -
autoText | string | 'meituan.com'
time | string | "11:00"
date | int | 1484904327575
rangeDate | array | [1484904327575,1487904237575]
select、treeSelect | object | {id:'12',name:'美团点评'}
checkbox | array | [1,2]
radio | string 、boolean 、int | Y
upload | array | ```[{$idField:1,$nameField:'附件',$urlField:'http://...'}]```

#### <span id="events>Model.events</span>

bill中配置内置事件 ，支持的内置事件有 bind、fetch、control三种<br/>
bind：下拉等自动获取数据集，通过配置一个url和自动获取参数的方式 在下拉组件需要获取数据集的时候，自动获取请求参数，根据配置的url自动获取数据 <br/>
fetch：当一个字段值发生改变的时候，自动获取数据并覆盖当前bill中的关联数据，例如 选择部门，带出来 部门名称和部门负责人的诉求 <br/>
control：配置当一个字段值发生改变之后，级联控制其他字段 show(显示)、hide(隐藏)、disable(禁用)、editable(激活)、view(只读)、require(必填)、option(可选)等

参数 | 说明 | 类型 | 默认值
:---- | :----- | :---- | :----
code | 需要绑定事件的字段的code | string | -
type | 类型 bind、fetch和control | string | -
params| 请求数据需要额外发送bill中的其它字段code, 还有params中指定的字段, 对 `control` 事件无效 |array | [ ]
url | 请求数据的url, 对 `control` 无效 | string | -
filterField | 请求数据的当前字段的描述，主要解决 搜索要求发的字段名与当前字段code不一致时做的映射 只对 `bind` `fetch` 有效 | array[Object] | -
process | 字段值改变时控制某些字段的状态, 值对 `control` | array | [ ]
noClear | bind 事件去获取下拉数据时发送 params 里指定的参数, 当 params 里指定的任一参数值变化时, 默认会将当前控件的值清空, 此属性来控制是否不清空, 只对 `bind` 事件有效 | boolean | false
fields | 当前 code 标识的字段值改变时, 指定去触发哪几个字段的改变, 只对 `fetch` 事件有效；如果默认为空 则执行自动覆盖（请求过来的数据能对应上的全覆盖） | array | - 

#### Model.events.control

对control事件的 process的描述

参数 | 说明 | 类型 | 默认值
:---- | :----- | :---- | :----
condition | 匹配这条规则需要满足的条件 |string |-
show| 满足条件后控制哪些字段显示 | array | [ ]
hide| 满足条件后控制哪些字段隐藏 | array | [ ]
editable| 满足条件后控制哪些字段可编辑 | array | [ ]
disable| 满足条件后控制哪些字段禁用 | array | [ ]
view| 满足条件后控制哪些字段只读 | array | [ ]
require | 满足条件后控制哪些字段是必填的 | array | [ ]
option | 满足条件后控制哪些字段是非必填的 | array | [ ]




