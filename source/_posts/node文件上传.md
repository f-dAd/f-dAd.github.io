title: fs图片上传
date: 2021-11-02 10:49:32
tags: 

  - node
  - fs

categories:

- imhere开发

---

## fs.unlink 一直显示路径不存在问题

问题描述：由于用户头像支持修改上传，所以需要把前一张图片删除掉，就要查询用户图片名并分割拼接成文件地址，一开始用的是 'public/images'+文件名拼接成的字符串，一直显示找不到路径，以为是以当前项目路径为根路径的，实际是从用户根路径直接一路定位

解决：使用path拼接路径，先取得当前绝对路径,再定位到目标文件夹下并拼接上文件名

path.join(__dirname,'../public/images', filename)

## 关于图片从库中已经删除无法去重的问题

### **使用 fs.access检查文件是否存在**

fs.access 接收一个 mode 参数可以判断一个文件是否存在、是否可读、是否可写，返回值为一个 err 参数。

## path函数

### `path.join([...paths])`

- `...paths` [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type) A sequence of path segments

- Returns [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type)

`path.join()`把所有的`path` 片段(必须是string类型)使用特定于平台的分隔符作为分隔符(这里不论你使用的分割符是否正确都可以得到正确的路径) ,最后序列化路径

0长`path` 片段会被忽略.  `'.'`代表当前工作路径，'..'代表上一级工作路径

```js
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
// Returns: '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar');
// Throws 'TypeError: Path must be a string. Received {}'
```

### `path.parse(path)`

- `path` [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type)

- Returns [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)

`path.parse()返回包含路径属性的path`. 

- `dir` [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type)

- `root` [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type)

- `base` [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type)

- `name` [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type)

- `ext` [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#String_type)

For example, on POSIX:

```js
path.parse('/home/user/dir/file.txt');
// Returns:
// { root: '/',
//   dir: '/home/user/dir',
//   base: 'file.txt',
//   ext: '.txt',
//   name: 'file' }
```

On Windows:

```js
path.parse('C:\\path\\dir\\file.txt');
// Returns:
// { root: 'C:\\',
//   dir: 'C:\\path\\dir',
//   base: 'file.txt',
//   ext: '.txt',
//   name: 'file' }
```

