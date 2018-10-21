# 1.
## 1.1 循环调用
```javascript
// 每隔3s调用一次，全局函数，进来等待3s才调用，进来就调用可以再弄个全局的ajax
setInterval(function () {
    $.ajax({
        method: 'POST',
        url: "/tables/getFlg",
        error : function() {
            alert('刷新状态返回失败 ');
        },
        success : function(data) {
            // alert(data)
            document.getElementById("demo").innerHTML=statusHandler(data);
            if ("0235".indexOf(data) >= 0) {
                // alert('更改成功');
                document.getElementById('taskStart').className = "layui-btn";
                document.getElementById('taskStop').className = "layui-btn layui-btn-danger";
            }else if("16".indexOf(data) >= 0) {
                document.getElementById('taskStop').className = "layui-btn layui-btn-danger";
            }
        }
    });
}, 3000)
```