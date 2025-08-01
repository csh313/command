1. viper 能正确输出 ldap 配置（用 viper.GetStringMap("ldap")），但 config.Get() 结构体里只有 addr 字段有值，bindUserDn 和 bindPassword 都是空的。
toml 配置文件如下：
```
[ldap]
addr = "ldap://127.0.0.1:389"
bindUserDn = "cn=admin,dc=example,dc=com"
bindPassword = "admin"
```
结构体：
```
Ldap struct {
    Addr         string `toml:"addr"`
    BindUserDn   string `toml:"bind-user-dn"`
    BindPassword string `toml:"bind-password"`
} `toml:"ldap"`
```
原因：
viper.Unmarshal 默认用 mapstructure 标签，不认 toml 标签！
你的结构体字段是 BindUserDn，但 toml 里是 bind-user-dn，viper 默认不会自动做驼峰和中横线的映射。

解决方法：
方法1.修改结构体，使用mapstructure标签：
```
Ldap struct {
    Addr         string `mapstructure:"addr"`
    BindUserDn   string `mapstructure:"bind-user-dn"`
    BindPassword string `mapstructure:"bind-password"`
} `mapstructure:"ldap"`
```
方法2.修改配置文件，使用驼峰命名法：
```
[ldap]
  addr = "ldap://127.0.0.1:389"
  bindUserDn = "cn=admin,dc=io,dc=plus"
  bindPassword = "admin123"
```