# rim
目前是超级车商圈和超级资源表微信小程序的后台，主要是提供小程序的REST接口服务

## 启动方式
main方法入口类：RimApplication，注意在运行main方法之前需要配置Idea中configuration的Active Profiles项,并指向dev,具体操作流程可以点击[这里](http://ohegl1i2i.bkt.clouddn.com/%E5%9B%BE%E7%89%87.png)查看

## REST接口规范
### REST返回结果规范
在编写REST接口的时候，需将返回结果定义成Result（因为在进行登录校验、参数校验、异常处理时都是统一返回Result格式的），所以开发人员务必遵守此项
* 正确案例
```
    @RequireLogin
    @PostMapping("/hello")
    public Result<String> hello(@RequestBody @Valid CommonForm commonForm){
        return Result.getSuccessResult("hello");
    }

```
* 错误案例
```
    @RequireLogin
    @PostMapping("/hello")
    public BizResult<String> hello(@RequestBody @Valid CommonForm commonForm){
        return BizResult.getSuccessResult("hello");
    }

```

## 登录校验
超级车商圈和超级资源表登录机制：小程序用户在第一次进入小程序时，rim就会给用户默认创建一个账号
项目中已经对登录校验做了统一切面处理，所以无需在代码中增加用户校验代码（使用方法：controller方法上增加@RequireLogin注解，请求参数继承CommonForm）
* 使用案例
```
    @RequireLogin
    @PostMapping("/hello")
    public Result<String> hello(@RequestBody @Valid CommonForm form){
        // 此处可以获取到userId
        Long userId = form.getRimUserId();
        return Result.getSuccessResult("hello");
    }

```
* 注意：
1、请求参数必须继承CommonForm
2、必须在方法上加上@RequireLogin注解
 
## 参数校验
Rim项目对REST接口的请求参数做了统一的校验，在编写业务代码时无需对请求参数做校验（复杂校验除外）
* 使用案例
```
    @RequireLogin
    @PostMapping("/hello")
    public Result<String> hello(@RequestBody @Valid CommonForm commonForm){
        return Result.getSuccessResult("hello");
    }

```
* 注意：
1、请求体前需要@Valid注解
2、入参不能带Errors参数

## Http网络请求规范
Rim项目中使用Retrofit+OKHttp优化了http请求,如需加http请求接口可以在HttpService中扩展
* 使用案例
```
    1、定义接口的请求方式、请求参数和响应参数
        /**
         * 消息推送
         */
        @POST("https://api.weixin.qq.com/cgi-bin/message/wxopen/template/send")
        Call<String> wechatPush(@Query("access_token") String token, @Body WechatPushForm form);
    
    2、具体使用
        /**
        * 微信消息推送
        */
        public static String wechatPush(String token, WechatPushForm form){
           return RetrofitHelper.getExecuteBody(getService().wechatPush(token,form));
        }
    
```

## 多数据源的使用方式
Rim项目中引入了B2b库和Rim库两个数据源，主要是通过区分mapper包名的方式对不同数据源使用不同的DataSource
* mapper文件默认
```
   1、*Mapper.xml文件存放位置
        db_rim库中的存放在resource/mapper/rim/
        db_b2b库中的存放在resource/mapper/b2b/
   
   2、*Mapper.java文件存放位置
           db_rim库中的存放在dal/mapper/rim/
           db_b2b库中的存放在dal/mapper/b2b/
```
* 注意：如需使用generator来生成需要修改MyBatisGenConst中的DB_NAME的值,如图：
![http://ohegl1i2i.bkt.clouddn.com/test.png](http://ohegl1i2i.bkt.clouddn.com/test.png)


