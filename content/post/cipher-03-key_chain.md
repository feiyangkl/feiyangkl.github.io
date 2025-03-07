
---
title: "钥匙串"
date: "2018-03-21 10:29:32"

tags: 
    - iOS
---

* 苹果的"生态圈"，钥匙串访问，使用 AES 256 加密算法，能够保证用户密码的安全
* 钥匙串访问SDK，是苹果在 `iOS 7.0.3` 版本以后公布的
* 钥匙串访问的接口是纯 C 语言的，但是，网络上有一个哥们把它封装成 OC 的，使用相当简单！
* 钥匙串访问的密码保存在哪里？
    * 只有苹果知道！是为了进一步保障用户的密码安全！
* 钥匙串访问的第三方框架，是对 C 框架的封装，可以不用看源代码
<!-- more --> 
## 框架地址

https://github.com/soffes/sskeychain

```objc
/// 所有账户
+ (NSArray *)allAccounts;
/// 获取所有账户信息
+ (NSArray *)accountsForService:(NSString *)serviceName;
/// 获取账号密码
+ (NSString *)passwordForService:(NSString *)serviceName account:(NSString *)account;
/// 删除账号密码
+ (BOOL)deletePasswordForService:(NSString *)serviceName account:(NSString *)account;
/// 将账号密码保存在钥匙串
+ (BOOL)setPassword:(NSString *)password forService:(NSString *)serviceName account:(NSString *)account;
```

## 保存钥匙串

```objc
///  保存用户信息
- (void)saveUserInfo {

    NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];

    // iOS 8.0 会直接写入沙盒，无需再使用 `[defaults synchronize];`
    [defaults setObject:self.username forKey:HMUsernameKey];

    // 将用户密码保存在钥匙串
    NSString *bundleId = [NSBundle mainBundle].bundleIdentifier;
    [SSKeychain setPassword:self.pwd forService:bundleId account:self.username];
}
```

## 从钥匙串加载密码

```objc
///  加载用户下信息
- (void)loadUserInfo {
    NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];

    self.usernameText.text = [defaults stringForKey:HMUsernameKey];

    NSLog(@"%@", [SSKeychain allAccounts]);
    NSString *bundleId = [NSBundle mainBundle].bundleIdentifier;
    self.pwdText.text = [SSKeychain passwordForService:bundleId account:self.usernameText.text];
}
```
