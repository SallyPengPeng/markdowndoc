## 背景

解析excel数据，前端或后端都可实现，考虑到当前需求为 导入并且预览表格数据，用户可对数据进行修改，最终提交的为用户修改后的数据，所以采用前端解析的方式更为合理。

## 依赖项

1、js-xlsx库：解析excel、写excel

github：https://github.com/SheetJS/sheetjs

2、react + ag-grid

## 步骤

> 默认已经搭建好了react脚手架，且支持ag-grid表格

#### 1、安装xlsx

```
npm install xlsx
```

#### 2、读取文件生成workbook

| function                           | explanation                            |
| ---------------------------------- | -------------------------------------- |
| XLSX.read(data, read_opts)         | attempts to parse `data`               |
| XLSX.readFile(filename, read_opts) | attempts to read `filename` and parse. |

```js
const { result } = event.target;
//  the entire excel spreadsheet object is read as a binary stream
const workbook = XLSX.read(result, { type: 'binary', cellDates: true });
```

workbook对象：

主要用到的有

1. SheetNames：所有的sheet名的数组
2. Sheets：所有的sheet内容的数组，是Sheet对象，需要在下一步解析
3. Workbook.Sheets[index].Hidden，sheet是否隐藏

![workbook对象](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-05-workbook%E5%AF%B9%E8%B1%A1-e2d0519a6c2d1a25c84486a7252090ac-049.png)

parsing Options(常用config):

| Option Name | Default | Description                               |
| ----------- | ------- | ----------------------------------------- |
| type        |         | Input data encoding，见第7步              |
| cellDates   | fasle   | Store dates as type `d` (default is `n`)  |
| sheets      |         | If specified, only parse specified sheets |

#### 3、解析worksheet

| function                                    | explanation                    |
| ------------------------------------------- | ------------------------------ |
| XLSX.utils.sheet_to_csv(sheet, config)      | generates CSV                  |
| XLSX.utils.sheet_to_txt(sheet, config)      | generates UTF16 Formatted Text |
| XLSX.utils.sheet_to_html(sheet, config)     | generates HTML                 |
| XLSX.utils.sheet_to_json(sheet, config)     | generates an array of objects  |
| XLSX.utils.sheet_to_formulae(sheet, config) | generates a list of formulae   |

使用 XLSX.utils.sheet_to_json将每一个sheet解析成对象，然后使用数据展示成表格或做其他处理

```js
const { Sheets: sheets } = workbook;
let parseData = Object.keys(sheets).reduce((finalData, sheetName) => {
    let sheetData = XLSX.utils.sheet_to_json(sheets[sheetName]);
    finalData.push({ sheetName, sheetData })
    return finalData;
}, []);
```

主要备注sheet_to_json的config（其他配置见github Utility Functions）：

| Option Name          | Default | Description                                        |
| -------------------- | ------- | -------------------------------------------------- |
| raw                  | true    | Use raw values (true) or formatted strings (false) |
| range（范围内取值）  | from WS | Override Range (see table below)                   |
| header（范围内取值） |         | Control output format (see table below)            |
| dateNF               | FMT 14  | Use specified date format in string output         |
| defval               |         | Use specified value in place of null or undefined  |
| blankrows            | false   | Include blank lines in the output                  |

`header` is expected to be one of:

| `header`         | Description                                  |
| ---------------- | -------------------------------------------- |
| `1`              | Generate an array of arrays ("2D Array")     |
| `"A"`            | Row object keys are literal column labels    |
| array of strings | Use specified strings as keys in row objects |
| (default)        | Read and disambiguate first row as keys      |

When `header` is not specified, the conversion will automatically disambiguate header entries by affixing `_` and a count starting at `1`. For example, if three columns have header `foo` the output fields are `foo`, `foo_1`, `foo_2`

```js
> XLSX.utils.sheet_to_json(ws);
[ { S: 1, h: 2, e: 3, e_1: 4, t: 5, J: 6, S_1: 7 },
  { S: 2, h: 3, e: 4, e_1: 5, t: 6, J: 7, S_1: 8 } ]

> XLSX.utils.sheet_to_json(ws, {header:"A"});
[ { A: 'S', B: 'h', C: 'e', D: 'e', E: 't', F: 'J', G: 'S' },
  { A: '1', B: '2', C: '3', D: '4', E: '5', F: '6', G: '7' },
  { A: '2', B: '3', C: '4', D: '5', E: '6', F: '7', G: '8' } ]

> XLSX.utils.sheet_to_json(ws, {header:["A","E","I","O","U","6","9"]});
[ { '6': 'J', '9': 'S', A: 'S', E: 'h', I: 'e', O: 'e', U: 't' },
  { '6': '6', '9': '7', A: '1', E: '2', I: '3', O: '4', U: '5' },
  { '6': '7', '9': '8', A: '2', E: '3', I: '4', O: '5', U: '6' } ]

> XLSX.utils.sheet_to_json(ws, {header:1});
[ [ 'S', 'h', 'e', 'e', 't', 'J', 'S' ],
  [ '1', '2', '3', '4', '5', '6', '7' ],
  [ '2', '3', '4', '5', '6', '7', '8' ] ]
```

#### 4、处理数据到worksheet

| function                                | explanation                                                  |
| --------------------------------------- | ------------------------------------------------------------ |
| XLSX.utils.aoa_to_sheet(data, config)   | converts an array of arrays of JS data to a worksheet.       |
| XLSX.utils.json_to_sheet(data, config)  | converts an array of JS objects to a worksheet.              |
| XLSX.utils.table_to_sheet(data, config) | converts a DOM TABLE element to a worksheet.                 |
| XLSX.utils.sheet_add_aoa(data, config)  | adds an array of arrays of JS data to an existing worksheet. |
| XLSX.utils.sheet_add_json(data, config) | adds an array of JS objects to an existing worksheet.        |

主要备注json_to_sheet的config配置（其他配置见github Utility Functions）：

| Option Name | Default | Description                                        |
| ----------- | ------- | -------------------------------------------------- |
| header      |         | Use specified column order (default `Object.keys`) |
| dateNF      | FMT 14  | Use specified date format in string output         |
| cellDates   | false   | Store dates as type `d` (default is `n`)           |
| skipHeader  | false   | If true, do not include header row in output       |

```js
ar ws = XLSX.utils.json_to_sheet([
  { A:"S", B:"h", C:"e", D:"e", E:"t", F:"J", G:"S" },
  { A: 1,  B: 2,  C: 3,  D: 4,  E: 5,  F: 6,  G: 7  },
  { A: 2,  B: 3,  C: 4,  D: 5,  E: 6,  F: 7,  G: 8  }
], {header:["A","B","C","D","E","F","G"], skipHeader:true});
```

#### 5、处理worksheet到workbook

| function                                      | explanation                               |
| --------------------------------------------- | ----------------------------------------- |
| XLSX.utils.book_append_sheet(wb, ws, ws_name) | Add the worksheet to an existing workbook |
| XLSX.utils.book_new()                         | Creating a new workbook from scratch      |

#### 6、写入excel文件

| function                                 | explanation                                                  |
| ---------------------------------------- | ------------------------------------------------------------ |
| XLSX.write(wb, write_opts)               | attempts to write the workbook `wb`                          |
| XLSX.writeFile(wb, filename, write_opts) | attempts to write `wb` to `filename`. In browser-based environments, it will attempt to force a client-side download |
| XLSX.writeFileAsync(filename, wb, o, cb) | attempts to write `wb` to `filename`. If `o` is omitted, the writer will use the third argument as the callback. |

writing Options(常用config):

| Option Name | Default | Description                                |
| ----------- | ------- | ------------------------------------------ |
| type        |         | Output data encoding，见第7步              |
| cellDates   | fasle   | Store dates as type `d` (default is `n`)   |
| sheets      |         | Name of Worksheet for single-sheet formats |

#### 7、各种type

read 和 write时type-读取类型:

| `type`     | expected input                                             |
| ---------- | ---------------------------------------------------------- |
| `"base64"` | string: Base64 encoding of the file                        |
| `"binary"` | string: binary string (byte `n` is `data.charCodeAt(n)`)   |
| `"string"` | string: JS string (characters interpreted as UTF8)         |
| `"buffer"` | nodejs Buffer                                              |
| `"array"`  | array: array of 8-bit unsigned int (byte `n` is `data[n]`) |
| `"file"`   | string: path of file that will be read (nodejs only)       |

单元格 原始值存在value中，类型存在type中，所以使解析或者写入时，可以设置类型：

| Type | Description                                                  |
| ---- | ------------------------------------------------------------ |
| `b`  | Boolean: value interpreted as JS `boolean`                   |
| `e`  | Error: value is a numeric code and `w` property stores common name ** |
| `n`  | Number: value is a JS `number` **                            |
| `d`  | Date: value is a JS `Date` object or string to be parsed as Date ** |
| `s`  | Text: value interpreted as JS `string` and written as text ** |
| `z`  | Stub: blank stub cell that is ignored by data processing utilities ** |

#### 8、常见问题

1、xlsx解析文件日期比实际少一天

解决办法：读取文件时，添加cellDates: true，使日期类cell读取后是date格式，在输出前，将所有date格式的cell统一加一天转为string

```js
// excel中设置了日期格式的列，统一加1天，并格式化为'YYYY-MM-DD'字符串，便于统一处理
row[item] = moment(row[item]).add(1, 'days').format('YYYY-MM-DD');
```

2、若日期修改后，变成时间戳，导出时需转换

日期、下拉框类cell编辑，ag表格配置valueParser属性无效，所以使用valueGetter + valueSetter

```js
{
    headerName: '日期格式',
    field: 'date',
    type: ['date', 'dateEditor'],
    valueGetter: function (params) {
        return params.data.date;
    },
    valueSetter: (params) => {
        const { newValue, oldValue } = params;
        let valueChanged = oldValue !== newValue;
        if (valueChanged) {
            params.data.date = convertToDateString(newValue);
        }
        return valueChanged;
    },                 
},
```

3、只需要读取未隐藏的工作表

XLSX.read读取得到的对象是wb

```
wb.Workbook.Sheets[index].Hidden // Hidden为0代表未隐藏，Hidden为1代表隐藏
```

## 参考

https://www.codestudyblog.com/sfb20react1/0306015318.html