# CRUD datagrid以及分页的使用要点

首先引入页面代码，其中一些属性、方法可以查看easyUI官网。

    <%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Build CRUD DataGrid with jQuery EasyUI - jQuery EasyUI Demo</title>
        <link rel="stylesheet" type="text/css" href="http://www.jeasyui.com/easyui/themes/default/easyui.css">
        <link rel="stylesheet" type="text/css" href="http://www.jeasyui.com/easyui/themes/icon.css">
        <link rel="stylesheet" type="text/css" href="http://www.jeasyui.com/easyui/demo/demo.css">
        <script type="text/javascript" src="http://code.jquery.com/jquery-1.6.min.js"></script>
        <script type="text/javascript" src="http://www.jeasyui.com/easyui/jquery.easyui.min.js"></script>
        <script type="text/javascript" src="http://www.jeasyui.com/easyui/jquery.edatagrid.js"></script>
    </head>
    <body>
    <h2>CRUD DataGrid</h2>
    <p>Double click the row to begin editing.</p>

    <table id="dg" title="My Users" style="width:700px;height:250px"
           toolbar="#toolbar" pagination="true" idField="id"
           rownumbers="true" fitColumns="true" singleSelect="true"
           >
        <thead>
        <tr>
            <th field=authorname width="50" editor="{type:'validatebox',options:{required:true}}">作者</th>
            <th field="origin" width="50" editor="{type:'validatebox',options:{required:true}}">来源</th>
        </tr>
        </thead>
    </table>
    <div id="toolbar">
        <a href="javascript:void(0)" class="easyui-linkbutton" iconCls="icon-add" plain="true"  onclick="javascript:$('#dg').edatagrid('addRow')">添加</a>
        <a href="javascript:void(0)" class="easyui-linkbutton" iconCls="icon-remove" plain="true" onclick="javascript:$('#dg').edatagrid('destroyRow')">Destroy</a>
        <a href="javascript:void(0)" class="easyui-linkbutton" iconCls="icon-save" plain="true" onclick="javascript:$('#dg').edatagrid('saveRow')">Save</a>
        <a href="javascript:void(0)" class="easyui-linkbutton" iconCls="icon-undo" plain="true" onclick="javascript:$('#dg').edatagrid('cancelRow')">Cancel</a>
    </div>
    <script type="text/javascript">
        $(function(){
            $('#dg').edatagrid({
                url: "findNewsByTopic.do?topic=111",//查询数据请求地址，换成你具体的controller映射地址
                saveUrl: 'save_user.php',
                updateUrl: 'update_user.php',
                destroyUrl: 'destroy_user.php',
            });
        });
    </script>
    </body>
    </html>
    
需要注意的是，easyUI使用jquery ajax与后台进行数据的传输，所以controller必须返回一个json数据才行，那怎么样实现数据的分页展示呢？我们看下controller代码。

    @RequestMapping(value = "/findNewsByTopic.do")
    public void findNewsByTopic(@RequestParam String topic, @RequestParam int page, @RequestParam int rows, HttpServletResponse     response, HttpServletRequest request) {
      int index = (page-1) * rows;
      System.out.println(page);
      List<News> list = new ArrayList<News>();
      list = newsDao.findNewsByTopic(topic, index, rows);
      JSONObject jsonObject = new JSONObject();
      jsonObject.put("total", newsDao.getCount(topic));
      jsonObject.put("rows", list);
      System.out.println(JSON.toJSONString(jsonObject));

      response.setContentType("application/json; charset=UTF-8");
      try {
        response.getWriter().print(JSON.toJSONString(jsonObject));
      } catch (IOException e) {
        // TODO Auto-generated catch block
        e.printStackTrace();
      }
    }
    
第一个要点就是一定要接收到easyUI传过来的两个参数，**page（当前第几页）与rows（每页显示的数据有多少行）**，第二利用easyUI动态传回的参数进行数据库的分页查询，第三把查询到的数据转为json数据回传并且一定要把查询到的**数据总条数命名为"total"回传**才行。
