# QBaseNetOperation

QBaseFramework网络请求类, 负责http网络请求任务。

## 如何使用QBaseNetOperation进行网络请求

### 使用须知（约定）

1. 在使用过程中，你将会创建很多网络请求类，每一个类都将对应着一个接口的任务。
*  每一个请求类，必须创建对应的数据模型类。

### 使用方式简要介绍

1. **必须__** 继承QBaseNetOperation，创建一个请求接口任务类
*  **必须__** 在函数`- (void)Config`中，重新配置你的接口属性
*  **必须__** 继承QBaseModel，创建一个匹配的数据模型类
*  **必须__** QBaseNetOperation需要设置代理 （代理必须为QBaseViewController子类）

## QBaseNetOperation详解

### 默认配置

| 字段名              | 字段介绍          | 默认配置属性 |
| -------------------|------------------|-----------|
| url                | 请求链接          | nil       |
| params             | 请求参数          | nil (字典) |
| method             | 请求方式          | @"GET"    |
| responseDataTag    | 响应数据标签       | @"data"   |
| isArchiveCache     | 是否数据缓存开关    | YES       |
| isDatabaseCache    | 是否数据库缓存开关  | YES       |
| isLoadingLocalJson | 是否读取本地模拟数据 | NO       |
| localJsonPath      | 模拟Json文件路径   | nil       |

### 关于本地数据模拟设置 

宏定义 `IS_LOADING_LOCAL_JSON` 

* 定义为 1
	* 全局调用模拟数据

* 定义为 0
	* 局部调用模拟数据, 需要在独立的测试里设置参数`isLoadingLocalJson`

### 继承配置函数Config

如果不是默认选项, 重写函数`-（void）config`进行配置， 例如：

```
- (void)config
{
	self.url = @"http://www.baidu.com";
	self.method = @"POST";
	self.isArchiveCache = NO;
	self.isDatabaseCache = NO;	
}
```

### 配置数据模型

#### 注意须知

1. 在配置前必须确认服务器返回的列表数据源的Json标签，如果不是data，请手动改写标签内容。
*  数据模型初始化时，数据模型属性必须与字典吻合，否则将会出现创建失败。

### 关于代理

代理函数分别为下载成功，下载失败。 如下所示：

```
@class QBaseNetOperation;
@protocol QBaseNetOperationDelegate <NSObject>

- (void)netOperationDidFinish:(QBaseNetOperation *)operation;
- (void)netOperationDidFailed:(QBaseNetOperation *)operation;

@end
```

#### 一般如何使用代理函数

| Operation回调常用字段 | 字段介绍               | 字段设计初衷  |
|---------------------| ----------------------|-------------|
| responseData        | 数据字典, 服务器获取Json | 查询获取信息  |
| dataArray           | 模型数组, 模型对象数组   |  完整数据源   |
| error               | 请求错误信息            |  失败错误信息 |

在这里必须要注意的是，如果模型配置有误，原因可能是**配置字段有误**。

### 外部如何调动

```
- (void)login
{
    QBaseLoginOperation *operation = [QBaseLoginOperation operationWithDelegate:self];
    
	  // 请求链接会在QBaseLoginOperation中设置
	  
	  // 设置参数
	  operation.params = @{
	  									@"username":@"admin",
	  									@"password":@"admin"
	  									}
	  									
	  // 设置请求体
    [operation setBodyBlock:^(id<AFMultipartFormData> formData) {
        [formData appendPartWithFileData:imageData
                                    name:@"test"
                                fileName:@"test.png"
                                mimeType:@"image/jpeg"];
    }];
    [operation start];
}

```
