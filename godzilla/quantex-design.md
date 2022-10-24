## ConfigProvider

使用react的context属性提供一些全局配置

```
userInfo: {},
dictConfig: defaultDictConfig,
agConfig: {},
getPrefixCls:() => ();
```

## ag-grid

### 自动计算表格高度

```javascript
calcHeight = debounce(() => {
  // 参考现在表格计算高度的做法
  // 一般公式就是：页面高度 - 表格左上角到顶部的高度
  const tableNode = this.tableRef.current; // 作用用html元素时，是dom节点
  const tableHeight = this.calcScrollYAuto(tableNode);

  this.setState({ height: tableHeight }); // height添加行内样式放在表格的顶级div样式
}, 100);
```

```tsx
calcScrollYAuto = (tableNode: any) => {
  const defaulConfig = {
    paginationHeight: 44, // 分页条高度
  };

  const { pagination } = this.props.tableConfig.props;
  // 表格距离底部的额外高度
  const { extraPaddingBottom = 0 } = this.props.tableConfig;

  // 当前 dom 距离上方的距离
  const [height, topDistance] = this.getCalcHeightWrapperInfo();
  // 高度逻辑计算:
  // 1. 以外层样式类(A)为基准, 计算 A.height
  // 2. 额外两个基准点: Table.top   and   A.top , 差值等于 Table 距离 A 的距离
  // 3. 表格高度: A.height - (Table.top - A.top)
  let padTableBottom: number;
  if (height) {
    padTableBottom = 12 + extraPaddingBottom;
    if (pagination) {
      padTableBottom += defaulConfig.paginationHeight;
    }
    return height - topDistance - padTableBottom;
  }

  const { top } = tableNode.getBoundingClientRect();
  const windowHeight = window.innerHeight;
  padTableBottom = 20 + 20 + 28 + 2 + extraPaddingBottom;
  // 如有分页
  if (pagination) {
    padTableBottom += defaulConfig.paginationHeight;
  }

  return windowHeight - top - padTableBottom;
};
```

```tsx
getCalcHeightWrapperInfo = () => {
  let height;
  let topDistance;
  let targetEl;
  // 如果 父级元素 已经找到, 不再重复查找
  if (this.calcHeightWrapperEl) {
    [height, topDistance] = this.getCalcHeightWrapperRect(this.calcHeightWrapperEl);
  } else {
    const { calcHeightClassName } = this.props.tableConfig;
    const tableNode = this.tableRef.current;
    if (tableNode) {
      let parentEl = tableNode.parentElement;
      const matchClassName = calcHeightClassName || `ag-calc-height-wrapper`;
      // 循环父节点, 直到找到 指定的类名
      while (parentEl) {
        const className = parentEl.className;
        if (className && className.includes(matchClassName)) {
          [height, topDistance] = this.getCalcHeightWrapperRect(parentEl);
          targetEl = parentEl;
          break;
        }
        parentEl = parentEl.parentElement;
      }
    }
  }

  this.addResizeEventHandler(targetEl);

  return [height, topDistance];
};

// 获取表格 指定类名父级 的距离信息: 父级高度, 表格距离父级的距离
getCalcHeightWrapperRect(el: Element) {
  const { top } = this.tableRef.current.getBoundingClientRect();
  const { height, top: wrapperElTop } = el.getBoundingClientRect();
  return [height, top - wrapperElTop];
}
```

