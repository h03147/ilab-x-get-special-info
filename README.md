# ilab-x-get-special-info
纯前端vue分析ilab-x虚拟仿真实验教学平台的api，提取我需要的数据。

功能：根据学科分类，页码和一页显示数量，取分类爬出每个学科大类下面的学校名称和项目名称，然后excel导出

注意首次运行安装完node_modules后要修改源码：
在 \node_modules\xlsx-style\dist\cpexcel.js找到 var cpt = require(’./cpt’ + ‘able’); 并改成 var cpt = cptable;
否则项目会报错。

**项目运行截图
![image](https://github.com/h03147/ilab-x-get-special-info/blob/main/public/img/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20210228151116.png)
