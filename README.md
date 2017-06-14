# -node.js-elasticsearch-
介绍用node.js做elasticsearch的步骤
<p>1.你必须要先配置elasticsearch所需要的java环境,不过多介绍配置java环境的步骤,网上会有很多教程,在java环境配置完成之后,再在你所在项目中安装node.js提供的官方模块：</p>
<p>npm install elasticsearch --save</p>
<p>2.然后你可以在脚本中导入模块：</p>
<p>const elasticsearch = require('elasticsearch');</p>
<p>3.你需要创建客户端来处理与弹性搜索的通讯。在这种情况下，我假设你正在运行弹性搜索的本地机器IP地址是127.0.0.1，端口是9200（默认设置）：</p>
<p>const esClient = new elasticsearch.Client({</p>
<p>  host: '127.0.0.1:9200',</p>
<p>  log: 'error'</p>
<p>  });</p>

// index.jsconst bulkIndex = function bulkIndex(index, type, data) {
  let bulkBody = [];

  data.forEach(item => {
    bulkBody.push({
      index: {
        _index: index,
        _type: type,
        _id: item.id      }
    });

    bulkBody.push(item);
  });

  esClient.bulk({body: bulkBody})
  .then(response => {
    console.log('here');
    let errorCount = 0;
    response.items.forEach(item => {
      if (item.index && item.index.error) {
        console.log(++errorCount, item.index.error);
      }
    });
    console.log(
      `Successfully indexed ${data.length - errorCount}
       out of ${data.length} items`
    );
  })
  .catch(console.err);};const test = function test() {
  const articlesRaw = fs.readFileSync('data.json');
  bulkIndex('library', 'article', articles);};
