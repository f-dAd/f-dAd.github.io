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

## 图片一次上传之后就无法继续上传

使用el-uploade的时候因为要限制图片只能上传一张，所以额外定义了上传文件列表，但上传成功后没有清空

```vue
      <el-upload
        class="up_portrait_box"
        action="/user/upload"
        :file-list="fileList"
        :show-file-list="false"
        :on-success="handleSucess"
        :on-exceed="handleExceed"
        :before-upload="beforeAvatarUpload"
        :limit="1"
        accept=".jpg, .jpeg, .png"
      >
        <!-- <el-tooltip
          content="上传头像，500kb以内的image/jpeg/jpg/png文件"
          placement="right"
        ></el-tooltip
        > -->
        <div class="up_portrait_mask">
          <i class="el-icon-camera"></i>
        </div>
        <Avatar
          :size="60"
          :url="friendinfo.portrait"
          :text="friendinfo.username"
          style="margin-right: 40px"
        />
      </el-upload>
```



```js
		// 文件超出限制时处理的函数
    handleExceed(files, fileList) {
      // 当上传文件数最大设置为1,再次选择新文件时会触发这个方法，利用这个对文件进行重新赋值即可
      // files为当前选择的文件，fileList已经选择后的文件（上次选择的）
      // 可以对fileList进行赋值来修改上传时的文件。但是只修改fileList是不够的，
      // 还需要修改el-upload里的uploadFiles，保持数据同步，不然上传后的回调函数里的字段会报错。这里就是自定义的this.fileslist
      // 多选时选最后一个文件
      this.fileList = files[files.length - 1]
    },
    // 限制上传头像文件的大小和类型
    beforeAvatarUpload(file) {
      const isJPG =
        file.type === 'image/jpeg' ||
        file.type === 'image/png' ||
        file.type === 'image/jpg'
      const isLt2M = file.size / 1024 / 1024 < 1

      if (!isJPG) {
        this.$message.error('上传头像图片只能是 JPG/JPEG/PNG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传头像图片大小不能超过 1MB!')
      }
      return isJPG && isLt2M
    },
    // 上传文件
    handleSucess(response, file, fileList) {
      if (response.errno === 0) {
        this.$message.success('图像修改成功')
        this.friendinfo.portrait = response.message
        // 清空文件列表,准备下次上传
        this.fileList = []
      }
    }
```

## 图片不存在时使用用户名做头像

```vue
 <el-avatar v-bgc="(text, this)" :size="size"  class="avatar" :key="url" :src="url" ref="image"  :onerror="imgLoadError()">  {{ username | cutName }}</el-avatar>
   
 
```

```js
// 自定义命令，用于随机生成背景颜色
directives: {
    bgc: {
      // 使用时给标签添加v-bgc就可以了
      //自定义命令里是获取不到this值的因此需要外部传递，还可以传递数值
      //通过 binding.value.参数名 获取
      inserted(el, binding) {
        // 配色方案，可自行修改，这里每次颜色不固定，观感较差
        // 准备利用用户名首字符映射的方式，这样防止颜色更改
        // 获取名字第一个文字，转换成16进制颜色值
        const firstName = binding.value.text.substring(1, 0)
        console.log(firstName)

        // tranColor = (name) => {
        //  var str ='';
        //  for(var i=0; i<name.length; i++) {
        //   str += parseInt(name[i].charCodeAt(0), 10).toString(16);
        //  }
        //  return '#' + str.slice(1, 4);
        // }
        // const bgColor = this.tranColor(name)
        // 这样就可以生成一个合法的16进制颜色字符串，如果需要配置不同的透明度，可以多取一位 str.slice(1, 5)，因为这里转换为16进制，所以这里只取前3位（1 ～ 4）
        const colorArray = [
          '63B4B8',
          'CE97B0',
          '19B3B1',
          'A7BBC7',
          '7952B3',
          '5580A0',
          'E5BB4B',
          'CC8A56',
          'A6C2CE',
          'BFA2DB',
          'B5525C'
        ]
        const a = () => Math.floor(Math.random(firstName) * colorArray.length)
        const rand = a()
        el.style.background = '#fff'
        el.style.border = '1px solid #' + colorArray[rand]
        el.style.color = '#' + colorArray[rand]
        // el.style.background = `rgb(${a()},${a()},${a()})`
      }
    }
  },
  methods: {
    //  头像加载失败就用用户名代替
    async imgLoadError() {
      // 注意，setTimeout()如果用到 this ，必须在函数外定义一个变量来暂存 this 。
      const that = this
      // 刚开始用户信息没取到，有可能是网络加载慢延时一会
      setTimeout(function () {
        that.avaurl = ''
        that.username = that.text
        console.log(that.username)
      }, 500) // 0.5秒
    }
  },
  filters: {
    cutName: function (value) {
      let res = value
      if (value.length <= 2) {
        return res
      }
      if (value.length === 4) {
        res = value.slice(2, -1)
      } else {
        res = value.slice(-2)
      }
      return res
    }
  }
```

### charCodeAt\fromCharCode

charCodeAt方法返回字符串指定位置的 Unicode 码点（十进制表示），相当于String.fromCharCode()的逆操作。

```js
'abc'.charCodeAt(1) // 98   abc的1号位置的字符是b，它的 Unicode 码点是98。
```

String.fromCharCode()
String对象提供的静态方法（即定义在对象本身，而不是定义在对象实例的方法），主要是String.fromCharCode()。该方法的参数是一个或多个数值，代表 Unicode 码点，返回值是这些码点组成的字符串。

```js
String.fromCharCode() // “”
String.fromCharCode(97) // “a”
String.fromCharCode(104, 101, 108, 108, 111) // “hello”
```

