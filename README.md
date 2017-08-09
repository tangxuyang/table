### 功能介绍
应后端需要，后台的管理系统今后使用异步数据加载的形式。而表格是一个经常出现的元素，因此有必要封装成插件，jQuery.wktable就是这样一个产物。功能列表如下：
1. 异步加载数据
2. 同步加载数据
3. 分页、跳转到具体页、选择页大小
4. 列头排序
5. 支持两级列头合并
 
数据格式
jQuery.wktable要求后台返回的json数据要有如下格式：
```
{
    "status":"1",
    "message":"",
    "data":{
        "total":17,
        "totalPage":4,
        "pageIndex":1,
        "pageSize":10,
        "size":13,
        "contents":[
                       .....
        ]
    }
}
```
- status:标识本次查询是否成功，1表示成功，所有非1都是失败
- message:失败时返回的错误
- data.total:查询结果总数，用来分页的
- data.tatalPage:总页数，jQuery.wktable可根据total和pageSize自动计算，所以这个字段是可选的
- data.pageIndex:当前页码，可选
- data.pageSize:页大小，可选
- data.size:当前页返回的数据条数，可选
- contents:表格中显示的数据的数组
 
### 演示
假设后台返回的数据如下，这里只给出contents节点下的数据：
```
{
    ......
        "contents":[
            {"name":"mks","age":12,"country":"China"},
            {"name":"sum","age":24,"country":"China"},
            {"name":"john","age":122,"country":"China"},
            {"name":"tim","age":13,"country":"China"},
            {"name":"mars","age":12,"country":"China"},
            {"name":"mks","age":12,"country":"England"},
            {"name":"sum","age":24,"country":"China"},
            {"name":"john","age":122,"country":"China"},
            {"name":"tim","age":13,"country":"China"},
            {"name":"mars","age":12,"country":"China"},
            {"name":"mks","age":12,"country":"England"},
            {"name":"sum","age":24,"country":"China"},
            {"name":"john","age":122,"country":"China"},
            {"name":"tim","age":13,"country":"China"},
            {"name":"mars","age":12,"country":"China"},
            {"name":"mks","age":12,"country":"England"},
            {"name":"sum","age":24,"country":"China"}
            ......
        ]
.....
}
```
同时假设，有如下的dom结构：
```
<table id="myTable"></table>
```
接下来就可以使用jQuery.wktable了
```
<script src="jQuery.min.js"></script><!--jQuery.wktable是jQuery的插件，所以要引入jQuery，按照实际项目中位置引入 -->
<script src="jQuery.wktable.min.js"></script><!-- 按照实际项目中的位置引入–>
<script>
$(function(){
$('#myTable').wktable({
    columns:[
                {text:'姓名',field:'name'},
                {text:'年龄',field:'age'},
                {text:'国籍',field:'country'}
            ],
    url:'data.php',
    parse:function(data){//转换成jQuery.wktable需要的格式
        return {
                    pageInfo:{
                            pageIndex:data.data.pageIndex,
                            pageSize:data.data.pageSize,
                            total:data.data.total
                        },
                        items:data.data.contents
                    };
        }
 });
 
});
</script>
```
运行结果如下：

 
Options
在首次调用wktable时需要传入一个options对象，它支持如下的属性：
```
columns:[//表格列头配置属性
        {
            text:"姓名",//显示文本
            field:"name",//对应数据对象中的字段，接受函数，函数的参数有两个，第一个是本条数据对象，第二个是位序（从0开始），例如:function(row,index){}
            sortable:true,//是否支持排序
            subColumns:[],//子列，表头合并时使用
            sortField:'name',//排序字段，不是必须的，如果field字段是函数，就需要单独配置sortField字段告诉插件使用哪个字段进行排序
            otherProperty:'',//别的其他属性，会直接查到生成的dom上，比如想给该列添加.text类，就可以写成class:'text'
        }
    ],
    url:"",//获取数据的地址
    method:'get',//请求的method
    data:[],//提供同步数据
    sort:'',//排序字段
    sortType:'',//排序类型
    contentType:''//请求头中content-type
    uniqueId:''//后台中id的字段名称，会绑定到tr上的data-id
    autoRequestAtTheFirstTime:true,//在初始化表格是是否自动发送去请求，有的时候会需要先初始化表格，而不需要发送请求，等到具体事件触发后才调用search来查询
    dataStringify:true,//默认值true，标志ajax发送的数据是否进行JSON.stringify,只对POST有效
    params:{},//查询参数
    tableNavigation: {//表格底部导航配置
            displayTableNavigation: true, //是否显示表格导航
            displayPagination: true, //是否显示分页信息        
            displayPageJump: true, //是否显示页跳转
            displayPageSizeSelection: true, //是否显示页面大小选择框
            pageSizeSet: [10, 20, 50, 100, 200, 500], //页面大小集合
            paginationPageCount: 5, //分页中显示的页数
        },
    sortClasses:{//表头排序的样式
        sortAscClass:"",//升序的类名，默认是.sort-asc
        sortDescClass:"",//降序的类名，默认是.sort-desc
        sortClass:""//支持排序的列都具有的类名，默认是.sort
    },
    pageSize:10,//页大小
    pageInfoMapping:{//分页和排序参数的mapping
        pageSize:'pageSize',//页大小参数名称，默认是pageSize，如果后台服务接受的参数名是ps，则可修改成ps
        pageIndex:'pageIndex'//当前页参数名称
        sort:'sort',//排序字段参数名称
        sortType:'sortType' //排序类型参数名称，支持asc和desc
    },
    parse:function(data){//转换数据为符合插件使用的数据格式
     
    },
    ready:function(){//表格加载完成后触发的事件，可以定制化一些操作
     
    }, 
    error:function(msg){//请求失败触发的事件
     
    }
```
jQuery.wktable暴露了如下方法，供编程调用：
    - goto：跳转到指定页，接受一个整型参数
    - next：下一页，无参
    - prev：上一页，无参
    - refresh：刷新当前页，无参
    - setOptions：设置options
    - search：查询，接受对象参数
 
调用方式，这里假设，点击了查询按钮，按照name=“mks”查询，可以如下编写代码：
```
$('#myTable').wktable('search',{name:'mks'});
```
