  maven配置代理
  ---
  
```
  <proxies>
    <proxy>
      <id>my-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>xiaoboji</username>
      <password>123456</password>
      <host>proxy.xxx.com</host>
      <port>80</port>
      <nonProxyHosts>192.168.**|*.google.com</nonProxyHosts>
    </proxy>
  </proxies>
```
- proxies：下可以有多个proxy元素，如果你声明了多个proxy元素，则默认情况下第一个被激活的proxy会生效。
- id：这里申明了一个my-proxy的代理，
- active：的值为true表示激活该代理
- protocol：表示使用的代理协议，这里是http。
- host:：域名或者IP
- password：密码
- username：用户名
- password：密码
- nonProxyHost 用来指定哪些主机名不需要代理，可以使用 | 符号来分隔多个主机名。此外，该配置也支持通配符，如*.google.com表示所有以google.com结尾的域名访问都不要通过代理。