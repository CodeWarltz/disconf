
细节讨论
=======

## 解决 配置“不一致性读“ 问题 ##

**问题描述：**

应用系统的配置更新过程，它会涉及到多个配置项的更新，它不是一个原子过程。如果在配置更新的过程中，应用程序去读取配置，这里可能存在些“时间窗口”，从而导致不一致性读问题。

**解决方法：**

Disconf支持Web或非Web系统，对于这个问题，Web系统或非Web系统需要区分来看：

对于Web系统：

- 解决方法一：提供ThreadContext包，在每次请求一开始时都复制系统里的所有配置缓存，从而保证每次会话的数据的一致性。
- 解决方法二：提供ThreadContext包，每次请求都绑定一个版本号，如果读取时版本号不一致则报错，需要重新请求。
- 解决方法三：方法二的加强版，添加注解，每次只复制强一制性的配置缓存。
- 解决方法四：提供ThreadContext包，系统内保存有多个缓存层，读取时统一读取某个版本的缓存。每当配置更新时，缓存层增加。

   
第一种方法，代价太大。第二种方法，严重增加用户负担，第三种还是需要用户关心这个事情。我们将采用第四种方法。

对于非Web项目：

比较难解决非一致性读取的问题。因为它没有了会话这样一个概念。Apache的FileChangedReloadingStrategy Reload配置文件的方案也没有解决此问题。所以，我们打算放弃这方面的解决。但是，我们还是会提供一个简单却Ugly的解决方案：提供函数来标识用户读取配置的边界。用户可以放弃使用这个方案，但是我们不保证不会发生“不一致读'问题。