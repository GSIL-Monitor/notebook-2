# 1. 
## 1.1 layui 的确认取消
```javascript
layer.confirm('确认停止?', {
    icon: 3, title: '提示', yes: function (index) {
        //do something
        layer.close(index);
    },
    cancel: function (index, layero) {
        layer.close(index);
        // reload();// 可以在这里刷新窗口
    }
});
```

