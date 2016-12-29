# CRUD datagrid以及分页的使用要点

首先引入页面代码，其中一些属性、方法可以查看easyUI官网。
  
    
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
