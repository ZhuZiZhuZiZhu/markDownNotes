# 联运SDK协议升级以及登录流程梳理

需求文档链接：https://docs.corp.kuaishou.com/d/home/fcACfmXQJoRLVPpafwuFxSAIQ

需求大致分为三个部分：

1. 在Pass Token 中加入UID字段，并且创建白名单。白名单用来记录哪些appId可以获得UID。
2. 颁发名为 “VerifySession” 的新Token
3. 游戏厂商尝试获取用户信息时加入VerifySession的验证逻辑，未来GameToken将不再外传。

详细信息见需求文档。

在这里有必要详细梳理一下各个Token的作用以及关系。联运SDK中一共包括四个Token，分别是GameToken，RefreshToken，VerifySession以及Pass Token，我们一个一个来看。

**GameToken**：

GameToken是联运内部用来维护用户登录态的，权限很大，可以用来绑定快手账号，用户信息授权甚至删除账号等操作。在之前的逻辑中，GameToken会被传给游戏供应商，然后供应商使用GameToken来获取用户信息。但是这样存在安全隐患，因为供应商也可以使用Game Token请求删除接口，会导致严重的后果。

**RefreshToken：**

RefreshToken是随着GameToken一同下发的，过期时间较长。当Game Token过期之后用户再登录的时候可以使用Refresh Token来获取新的Token。

**VerifySession：**

GameToken权限过大，所以我们希望限制一下游戏厂商的权利。最好的办法就是不再把Game Token传播出去。VerifySession就是为了代替GameToken来进行访问权限限制的。

**Pass Token：**

是专门提供给客户端的一个map，其中包括了一些客户端需要的信息（不一定暴露给游戏公司），比如UID，accesstoken等。

## 1. 登录流程

## 2. 登录链路

登录接口分为匿名登录, 快手登录以及Refresh Token 登录。

### **2.1 匿名登录接口**

接口地址为：**“/game/anonymous”**

```java
//方法签名
public LoginDTO loginAnonymous(String appId, DeviceInfo deviceInfo)
```

其中的实现逻辑是首先去匿名库中查询是否存在这个用户，如果发现不是第一次登录就无需注册，直接登录即可。如果库中没有记录，就需要先注册，生成新的GameId并且加入数据库中。当获取到GameId之后，就可以进行Token的获取。

再跳过函数的嵌套之后，颁发Token的方法签名如下：

```java
public GameTokenDTO getLoginToken(GameId gameId, DeviceInfo deviceInfo, boolean needPassportToken)
```

其中GameId就是我们在之前获取到的，DeviceInfo是记录一些登录设备信息，而最后一个参数决定了是否需要PassPortToken。这个Token主要是给客户端传输数据用的。







### 2.2 快手账号登录接口

使用快手账号登录时，访问接口为：**“game/login”**

```java
//方法签名
public Map<String, Object> login(@RequestParam("platform") String platform, 
                                 @RequestAttribute(APP_ID) String appId,
                                 @RequestAttribute(DEVICE_INFO) DeviceInfo deviceInfo,
                                 @RequestBody String json) 
```

在login方法中，首先将需要的信息从json中取出。然后通过accountService.login()  方法来获取LoginDTO；
LoginDTO结构如下：

```java
public class LoginDTO {
    private String gameToken;
    private String refreshToken;
    private Map<String, Object> passToken;
    private String tokenSign;
    //加入新的token
    private String verifySession;
    //加入新的token的签名
    private String verifySessionSign;
    private GameUser gameUser;
    private String accessToken;
    private boolean newUser; // 是否新用户
} 
```

回到accountService.login() 方法，方法签名如下

```java
public LoginDTO login(String platform, String appId, DeviceInfo deviceInfo, Map<String, String> oauthParams)  
```

### 2.3 Refresh Token登录

## 3. 需求实现