## 一、简单客户端调用

```java
package com.api.connector.elasticsearch;

import org.apache.http.HttpHost;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/15 9:37 下午
 **/
public class ESClient {
    public static void main(String[] args) throws IOException {
        // 创建ES客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder( new HttpHost("localhost",9200,"http"))
        );

        // 关闭es客户端
        esClient.close();
    }
}
```

## 二、索引创建

```java
package com.api.connector.elasticsearch;

import org.apache.http.HttpHost;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.CreateIndexRequest;
import org.elasticsearch.client.indices.CreateIndexResponse;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 8:49 下午
 **/
public class ESIndexCreate {
    public static void main(String[] args) throws IOException {

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        //创建索引
        CreateIndexRequest request = new CreateIndexRequest("people");
        CreateIndexResponse createIndexResponse = esClient.indices().create(request, RequestOptions.DEFAULT);

        // 响应状态
        boolean acknowledged = createIndexResponse.isAcknowledged();
        System.out.println("索引操作： "+ acknowledged);

        esClient.close();

    }
}
```

## 三、索引查询&删除

### 1. 查询

```java
package com.api.connector.elasticsearch;

import org.apache.http.HttpHost;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.CreateIndexRequest;
import org.elasticsearch.client.indices.CreateIndexResponse;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.client.indices.GetIndexResponse;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 9:06 下午
 **/
public class ESIndexSearch {
    public static void main(String[] args) throws IOException {

        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        // 查询索引
        GetIndexRequest request = new GetIndexRequest("student");
        GetIndexResponse getIndexResponse = esClient.indices().get(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println(getIndexResponse.getAliases());
        System.out.println(getIndexResponse.getMappings());
        System.out.println(getIndexResponse.getSettings());

        esClient.close();

    }
}
```

### 2. 删除

```java
package com.api.connector.elasticsearch;

import org.apache.http.HttpHost;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest;
import org.elasticsearch.action.support.master.AcknowledgedResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.client.indices.GetIndexResponse;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 9:10 下午
 **/
public class ESIndexDelete {
    public static void main(String[] args) throws Exception{
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        // 查询索引
        DeleteIndexRequest request = new DeleteIndexRequest("people");
        AcknowledgedResponse response = esClient.indices().delete(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println(response.isAcknowledged());

        esClient.close();
    }
}
```

## 四、文档新增&修改

### 1. 新增

```java
package com.api.connector.elasticsearch.esDoc;

import com.api.connector.elasticsearch.bean.User;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.http.HttpHost;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 9:19 下午
 **/
public class ESDocInsert {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        //插入数据
        IndexRequest request = new IndexRequest();
        request.index("user").id("1001");

        User user = new User();
        user.setName("zhangsan");
        user.setAge(23);
        user.setSex("man");

        // 向ES插入数据，必须将数据转换为JSON格式
        ObjectMapper mapper = new ObjectMapper();
        String userJson = mapper.writeValueAsString(user);
        request.source(userJson, XContentType.JSON);

        IndexResponse response = esClient.index(request, RequestOptions.DEFAULT);
        System.out.println(response.getResult());

    }
}
```

### 2. 修改

```java
package com.api.connector.elasticsearch.esDoc;

import com.api.connector.elasticsearch.bean.User;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.http.HttpHost;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;

import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 9:48 下午
 **/
public class ESDocUpdate {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        //修改数据
        UpdateRequest request = new UpdateRequest();
        request.index("user").id("1001");
        request.doc(XContentType.JSON,"sex","women");

        UpdateResponse response = esClient.update(request, RequestOptions.DEFAULT);
        System.out.println(response.getResult());

        esClient.close();
    }
}

```

## 五、文档查询&删除

### 1. 查询

```java
package com.api.connector.elasticsearch.esDoc;

import org.apache.http.HttpHost;
import org.elasticsearch.action.get.GetRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 10:00 下午
 **/
public class ESDocGet {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

       // 查询数据
        GetRequest request = new GetRequest();
        request.index("user").id("1001");
        GetResponse response = esClient.get(request, RequestOptions.DEFAULT);

        System.out.println(response.getSourceAsString());

        esClient.close();
    }
}
```

### 2. 删除

```java
package com.api.connector.elasticsearch.esDoc;

import org.apache.http.HttpHost;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.get.GetRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 11:46 下午
 **/
public class ESDocDelete {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        // 查询数据
        DeleteRequest request = new DeleteRequest();
        request.index("user").id("1001");

        DeleteResponse response = esClient.delete(request, RequestOptions.DEFAULT);
        System.out.println(response.toString());
        esClient.close();
    }
}
```

### 3. 批量插入

```java
package com.api.connector.elasticsearch.esDoc;

import com.api.connector.elasticsearch.bean.User;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.http.HttpHost;
import org.elasticsearch.action.bulk.BulkRequest;
import org.elasticsearch.action.bulk.BulkRequestHandler;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;
import java.util.Arrays;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 11:56 下午
 **/
public class ESDocBatchInsert {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        // 批量插入数据
        BulkRequest request = new BulkRequest();
        request.add(new IndexRequest().index("user").id("1001").source(XContentType.JSON, "name", "zhangsan"));
        request.add(new IndexRequest().index("user").id("1002").source(XContentType.JSON, "name", "lisi"));
        request.add(new IndexRequest().index("user").id("1003").source(XContentType.JSON, "name", "wangwu"));

        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
        System.out.println(response.getTook());
        System.out.println(Arrays.toString(response.getItems()));

        esClient.close();

    }
}
```

### 4. 批量删除

```java
package com.api.connector.elasticsearch.esDoc;

import org.apache.http.HttpHost;
import org.elasticsearch.action.bulk.BulkRequest;
import org.elasticsearch.action.bulk.BulkResponse;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;
import java.util.Arrays;

/**
 * @author: Peiyang
 * @Date: 2021/12/23 10:53 下午
 **/
public class ESDocBatchDelete {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        // 批量插入数据
        BulkRequest request = new BulkRequest();
        request.add(new DeleteRequest().index("user").id("1001"));
        request.add(new DeleteRequest().index("user").id("1002"));
        request.add(new DeleteRequest().index("user").id("1003"));

        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
        System.out.println(response.getTook());
        System.out.println(Arrays.toString(response.getItems()));

        esClient.close();
    }
}
```

### 5. 高级查询

```java
package com.api.connector.elasticsearch.esDoc;

import com.api.connector.elasticsearch.bean.User;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.apache.http.HttpHost;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;

import org.elasticsearch.action.update.UpdateRequest;
import org.elasticsearch.action.update.UpdateResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.common.xcontent.XContentType;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/18 9:48 下午
 **/
public class ESDocUpdate {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        //修改数据
        UpdateRequest request = new UpdateRequest();
        request.index("user").id("1001");
        request.doc(XContentType.JSON,"sex","women");

        UpdateResponse response = esClient.update(request, RequestOptions.DEFAULT);
        System.out.println(response.getResult());

        esClient.close();
    }
}
```

#### 6. 条件查询

```java
package com.api.connector.elasticsearch.esDoc;

import org.apache.http.HttpHost;
import org.elasticsearch.action.search.SearchRequest;
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.index.query.QueryBuilder;
import org.elasticsearch.index.query.QueryBuilders;
import org.elasticsearch.search.SearchHit;
import org.elasticsearch.search.SearchHits;
import org.elasticsearch.search.builder.SearchSourceBuilder;

import java.io.IOException;

/**
 * @author: Peiyang
 * @Date: 2021/12/23 11:07 下午
 **/
public class ESDocQuery {
    public static void main(String[] args) throws IOException {
        RestHighLevelClient esCilent = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost",9200,"http"))
        );

        // 1. 条件查询
        SearchRequest request = new SearchRequest();
        request.indices("user");

        request.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("age",30)));
        SearchResponse response = esCilent.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println(hits.getTotalHits());
        System.out.println(response.getTook());

        for (SearchHit hit : hits){
            System.out.println(hit.getSourceAsString());
        }

        esCilent.close();

    }
}
```



