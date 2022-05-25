# Kwai Stannis Audio SDK  

## Stannis 
接入Android端需要stannis aar和ksaudioprocesslib aar
接入iOS端需要KWStannis.framework

stannis的接入文档：https://docs.qingque.cn/d/home/eZQBoEDJcuiilPFOcQfXNcMsO?identityId=1oEDz9dPp2a    

## 接入OpenAPI

### Android
需要licensing aar
Hint: 请保证OpenAPI的初始化时机早于Stannis  

    private void initSloManager() {
        SloManager.get().init(new InitParams() {
          @Override
          public String getHost() {
            return "https://vod.streamlakeapi.com";
          }
        
          @Override
          public String getAccessKey() {
            return "xxx"; // 对应你账户申请的AK
          }
        
          @Override
          public String getSecretKey() {
            return "secretKey"; //此字段为缺省字段, 目前可传任意字段
          }
        
          @Override
          public String getVersion() {
            return "2022-02-10"; //一般为Build日期
          }
        });
    }

### iOS
将iOS 的两个xcframework（StreamLakeLicensing、StreamLakeOpenAPI）集成到工程项目中，并完成以下功能配置：

#### 工程配置1
由于framework中含有objc类的分类(category)代码。需要配置如下build settings - other linker flags来保证分类方法符号可以被加载进来  
`$(OTHER_LDFLAGS) -ObjC.`  
https://developer.apple.com/library/archive/qa/qa1490/_index.html


#### 工程配置2
关闭bitcode


#### 初始化StreamLakeOpenAPI

1. 设置 Class<SLONetworkingSetup>协议实现  
    a. openAPI_AccessKey 返回对应申请好的AK  
    b. onlineHosts线上域名设置为https://vod.streamlakeapi.com  
    c. deployVersion设置为2022-02-10  

```
#import <StreamLakeOpenAPI/SLONetworkingSetupConfig.h>

@interface SLAppDelegate()<SLONetworkingSetup>
@end

@implementation SLAppDelegate

// 填写申请的AK
+ (NSString *)openAPI_AccessKey {
    return @"xx";
}

+ (NSString *)openAPI_DeployVersion {
    return @"2022-02-10";
}

+ (NSArray<NSString *> *)openAPI_OnlineHosts {
    return @[@"https://vod.streamlakeapi.com"];
}


- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      // 尽早设置 配置实现类
      [SLONetworkingSetupConfig setupNetworking:self.class];
    return YES;
}
```


