项目中使用antd的form，总是好奇使用`getFieldDecorator`包裹后的类似`names[${k}].name`形式的id，是如何解析成数组或对象的，所以从源码解析了原理并记录下来。

antd的form引用的是rc-form，所以实现的原理其实是在rc-form中，

github：https://github.com/react-component/form.git

以下解析仅专注于get 和 set时，如何解析入参id，对于从id获取对应表单value的部分暂不深究。

## set

rc-form中多次用到`lodash`的set，所以需要了解用法 ``_.set(object, path, value)``

```javascript
var object = { 'a': [{ 'b': { 'c': 3 } }] };
 
_.set(object, 'a[0].b.c', 4);
console.log(object);
// => { 'a': [{ 'b': { 'c': 4 } }] }
 
_.set(object, ['x', '0', 'y', 'z'], 5);
console.log(object);
// => { 'a': [{ 'b': { 'c': 4 } }], 'x': [{ 'y': { 'z': 5 } }] }
```

## 业务代码

假设最后需要获取的数据结构是这种 `{ a: 1, b: [{ c: 2, d: 2 }, { c: 3, d: 3 }] }`，则业务代码结构为如下格式

```javascript
……
// 假设有2组数据
const batchItem = [{ key: 0 }, { key: 1 }];
const c = `b[${key}].c`;
const d = `b[${key}].d`;
……
<Form onSubmit={this.handleSubmit}>
  <FormItem label="a_label">{getFieldDecorator("a")(<input />)}</FormItem>
  {batchItem.map((item, index) => {
    return (
      <React.Fragment key={item.key}>
        <FormItem label="c_label">{getFieldDecorator(c)(<input />)}</FormItem>
        <FormItem label="d_label">{getFieldDecorator(d)(<input />)}</FormItem>
      </React.Fragment>
    );
  })}
</Form>;
……
```

## getFieldValue

getFieldValue(fieldName) getFieldsName() 获取值逻辑相同，获取值的源码如下。以下注释根据不同的入参说明是如何解析入参id，从而获取到对应的值的

```javascript
/**
 *@params name 需要获取表单项的id
 *@params getter 获取id对应的value
 */
getNestedField(name, getter) {
  // 获取入参：需要获取表单项对应的id
  // 若 1. 入参：a => fullNames: ['a'] ， 入参：b[0].c => ['b[0].c']
  // 若 2. 入参：b[0] => fullNames: ['b[0].c', 'b[0].d']
  // 若 3. 入参：b => fullNames: ['b[0].c', 'b[0].d', 'b[1].c', 'b[1].d']
  const fullNames = this.getValidFieldsFullName(name);
  // 对应第一种入参类型，为fullName，只用获取一项值，则直接使用getter获取值返回 output：1
  if (
    fullNames.length === 0 || // Not registered
    (fullNames.length === 1 && fullNames[0] === name) // Name already is full name.
  ) {
    return getter(name);
  }
	
  // 入参name是partialName
  // 入参是第2种类型，isArrayValue为false, 路径只需定位到.后，即 c d
  // 入参是第3种类型，isArrayValue为true，路径需要定位到'['，即 [0].c [0].d [1].c [1].d
  const isArrayValue = fullNames[0][name.length] === '[';
  const suffixNameStartIndex = isArrayValue ? name.length : name.length + 1;

  // 使用lodash的set来获取最终输出值，对应第2、3种类型
  // 第2种：output：{ c: 2, d: 2 }
  // 第3种：output：[{ c: 2, d: 2 }, { c: 3, d: 3 }]
  return fullNames
    .reduce(
    (acc, fullName) => set(
      acc,
      fullName.slice(suffixNameStartIndex),
      getter(fullName)
    ),
    isArrayValue ? [] : {}
  );
}

```

获取fullNames的源码如下：

```javascript
getValidFieldsFullName(maybePartialName) {
    const maybePartialNames = Array.isArray(maybePartialName) ?
      maybePartialName : [maybePartialName];
    return this.getValidFieldsName()
      .filter(fullName => maybePartialNames.some(partialName => (
        fullName === partialName || (
          startsWith(fullName, partialName) &&
          ['.', '['].indexOf(fullName[partialName.length]) >= 0
        )
      )));
  }
```

## setFields

setFields和setFieldsValue原理相同，以下是源码。set值的时候，入参id可能经过多次递归，才能确定需要赋值的是 一个或多个表单项

```javascript
function treeTraverse(path = '', tree, isLeafNode, errorMessage, callback) {
  if (isLeafNode(path, tree)) {
    callback(path, tree);
  } else if (tree === undefined || tree === null) {
    // Do nothing
  } else if (Array.isArray(tree)) {
    tree.forEach((subTree, index) => treeTraverse(
      `${path}[${index}]`,
      subTree,
      isLeafNode,
      errorMessage,
      callback
    ));
  } else { // It's object and not a leaf node
    if (typeof tree !== 'object') {
      warning(false, errorMessage);
      return;
    }
    Object.keys(tree).forEach(subTreeKey => {
      const subTree = tree[subTreeKey];
      treeTraverse(
        `${path}${path ? '.' : ''}${subTreeKey}`,
        subTree,
        isLeafNode,
        errorMessage,
        callback
      );
    });
  }
}

/**
 * 获取实际赋值的表单项的id 、name对应的object
 * @param {*} maybeNestedFields 入参，即需要赋值的表单项
 * @param {*} isLeafNode 判断是否是fullName的方法
 * @param {*} errorMessage
 */
function flattenFields(maybeNestedFields, isLeafNode, errorMessage) {
  const fields = {};
  treeTraverse(undefined, maybeNestedFields, isLeafNode, errorMessage, (path, node) => {
    fields[path] = node;
  });
  return fields;
}
```

若`maybeNestedFields`: `{ a: 1, b: [{ c: 2, d: 2 }, { c: 3, d: 3 }] }`

从flattenFields方法中开始第一次treeTraverse调用

1. treeTraverse(undefined, { a: 1, b: [{ c: 2, d: 2 }, { c: 3, d: 3 }] }, ……)   => fields: {  }

2. treeTraverse(a, 1, ……)   => fields: { a: 1 }

3. treeTraverse(b, [{ c: 2, d: 2 }, { c: 3, d: 3 } }], ……)   => 触发更层递归调用
   1. treeTraverse(‘b[0]’,  { c: 2, d: 2 }, ……)  => 触发更层递归调用
      1. treeTraverse(‘b[0].c’,  2, ……)  => fields: { a: 1, ‘b[0].c’: 2 }
      2. treeTraverse(‘b[0].d’,  2, ……)  => fields: { a: 1, ‘b[0].c’: 2, 'b[0].d': 2 }  
   2. treeTraverse(‘b[1]’,  { c: 3, d: 3 }, ……)  => 触发更层递归调用
      1. treeTraverse(‘b[1].c’,  3, ……)  => fields: {  a: 1, ‘b[0].c’: 2, 'b[0].d': 2,  ‘b[1].c’: 3 }
      2. treeTraverse(‘b[1].d’,  3, ……)  => fields: { a: 1, ‘b[0].c’: 2, 'b[0].d': 2,  ‘b[1].c’: 3,  ‘b[1].d’: 3 } 

4. 第一次treeTraverse调用结束，return fields;

## 总结

```javascript
getFieldValue('a')   // 1
getFieldValue('b[0].c')   // 2
getFieldValue('b[0]')   // { c: 2, d: 2 }
getFieldValue('b')   // [{ c: 2, d: 2 }, { c: 3, d: 3 }]

setFieldValue({ a: 5 }) // a表单项赋值为5
setFieldValue({ b[0].c: 5 }) // 批量表单项的第一项的c赋值为5
setFieldValue({ b[0]: { c: 5, d: 5 } }) // 批量表单项的第一项的c赋值为5,d赋值为5
setFieldValue({ b: [{ c: 5, d: 5 }, { c: 6, d: 6 }] }) // 批量表单项的第一项c、d赋值为5；第2项c、d赋值为6
```



























