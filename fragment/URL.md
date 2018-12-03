# URL 

URL 为 Uniform Resource Locator(统一资源定位符) 的缩写, 用于标识英特网上资源存放路径.现在属于英特网标准 [RFC1738 提案](https://tools.ietf.org/html/rfc1738).



1. URL 的基本定义, 基于[RFC1808 提案](https://tools.ietf.org/html/rfc1808)

   ```shell
   <scheme>://<net_loc>/<path>;<params>?<query>#<fragment>
   
   scheme ":"   ::= scheme name, as per Section 2.1 of RFC 1738 [2].
   "//" net_loc ::= network location and login information, as per Section 3.1 of RFC 1738 [2].
   "/" path     ::= URL path, as per Section 3.1 of RFC 1738 [2].
   ";" params   ::= object parameters (e.g., ";type=a" as in Section 3.2.2 of RFC 1738 [2]).
   "?" query    ::= query information, as per Section 3.3 of RFC 1738 [2].
   "#" fragment ::= fragment identifier.
   ```

2. NSURL/URL

   apple 中URL采用 [RFC1808 提案](https://tools.ietf.org/html/rfc1808) ,所以在各语言交流中需要留意(android 默认采用 [RFC 2396](https://www.ietf.org/rfc/rfc2396.txt)/[RFC2732](https://www.ietf.org/rfc/rfc2732.txt)),



1. URLComponents

   URLComponents 采用的 [RFC3986 提案](https://tools.ietf.org/html/rfc3986) 设计,