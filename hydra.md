# hydra

[项目链接](https://github.com/vanhauser-thc/thc-hydra)

介绍:



### 安装:

 - ####macOS

   1. 安装 [**`brew`**](https://docs.brew.sh/Installation):

      ```shell
      mkdir homebrew && curl -L https://github.com/Homebrew/brew/tarball/master | tar xz --strip 1 -C homebrew
      ```

   2. 安装`hydra`:

      ```shell
      # 以下2步最好执行一遍,否则可能遇到 
      # [ERROR] Compiled without LIBSSH v0.4.x support, module is not available!
      brew search hydra
      brew info hydra
      # 安装带ssh访问版本
      brew install hydra --with-libssh
      ```

- #### linux

  1. 由于密码文件太大,还没试过.
  2. 可参照[官方文档操作](https://github.com/vanhauser-thc/thc-hydra)

###使用:

- 使用简介
  1. 



###问题:

1. `[ERROR] Compiled without LIBSSH v0.4.x support, module is not available!`

   ```shell
   # 笔者在使用 brew 安装之前,安装官网上编译安装了一遍.
   # 导致 /usr/local/hydra 存在
   # 解决方案:
   # 移除 make install 中 cp 的所有文件
   # 在使用 brew 安装
   ```

2. `[ERROR] Maximum number of passwords is 50000000, this file has 4103549346 entries.`

   ```shell
   # hydra 的单密码文本限制为 50000000 行,但是常规的密码文本远超于这个数字.
   # 解决方案:
   # 利用shell split 函数按行分割原始密码文本后遍历执行 hydra:
   
   split -a 4 -l 5000000 [目标文件] [输出文件前缀]
   # -a 序号长度, 默认 2 位
   # -l 单文件最大行数
   
   for user in words_*
   do
   	for pass in words_*
   	do
   		hydra [[options] ...] -L "${user}" -P "${pass}" [[args] ...]
   	done
   done
   rm -v words_*
   ```
