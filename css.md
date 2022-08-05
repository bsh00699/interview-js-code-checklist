#### div水平且垂直居中
给一个具有宽高的div,实现它在页面上水平且垂直居中
* 利用margin:auto,实现绝对元素的居中
```
<!DOCTYPE html>
<html lang="en">
<style>
  body {
    background-color: honeydew;
  }

  .content {
    width: 200px;
    height: 200px;
    background-color: aquamarine;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    position: absolute;
    margin: auto;
  }
</style>

<body>
  <div class="content"></div>
</body>
</html>
```