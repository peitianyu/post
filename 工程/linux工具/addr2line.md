---
title: addr2line
date: 2022-5-11 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - tool
---

# 参考网址:

 [(132条消息) addr2line 输出为?:0可能原因_qq_23101811的博客-CSDN博客](https://blog.csdn.net/qq_23101811/article/details/122184548) 

 [(132条消息) addr2line命令_残阳的博客-CSDN博客_addr2line命令用法](https://blog.csdn.net/lhf_tiger/article/details/9088609) 

 [(132条消息) Linux命令之dmesg命令_浪子吴天的博客-CSDN博客_dmesg](https://blog.csdn.net/carefree2005/article/details/120737841?ops_request_misc=%7B%22request%5Fid%22%3A%22165224592316781683954060%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165224592316781683954060&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-120737841-null-null.142^v9^pc_search_result_cache,157^v4^control&utm_term=dmesg&spm=1018.2226.3001.4187) 

 [(133条消息) addr2line排查地址_AK_Coffee的博客-CSDN博客_addr2line地址](https://blog.csdn.net/chi_wy/article/details/82023672?ops_request_misc=%7B%22request%5Fid%22%3A%22165232444016781667896542%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165232444016781667896542&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-82023672-null-null.142^v9^pc_search_result_cache,157^v4^control&utm_term=addr2line+-C&spm=1018.2226.3001.4187) 

# 使用流程

```sh
# 编译注意使用-g模式编译
gcc -o test -g test.c
#清除内核环形缓冲区(ring butter)
dmesg -C 
#执行文件
./test
# 查看所有开机日志信息
dmesg
[3540868.730553] traps: test[4331] trap divide error ip:55d347e5067b sp:7ffdb3499f50 error:0
[3540868.730567]  in test[55d347e50000+1000]
# 计算ip
ip: 55d347e5067b - 55d347e50000 = 067b
# addr2line -e test 067b
# addr2line -a -C -e libstagefright_omx.so  -f 00023808
/var/testhelper/programsymbols/tes.c:8
```



```sh
dmesg -C
cd bin && ./SlamTest
dmesg > /tmp/dmesg.txt

# 打印第一行
grep -m 1 -E "SlamTest\[[0-9]+\]: segfault" /tmp/dmesg.txt

# 读取第一行的ip值,并保存
addr_end=$(grep -m 1 -E "SlamTest\[[0-9]+\]: segfault" /tmp/dmesg.txt | awk -F 'ip ' '{print $2}' | awk -F ' ' '{print $1}')
# echo $addr_end
# 读取第一行的SlamTest值,并去除+后面的值
addr_begin=$(grep -m 1 -E "SlamTest\[[0-9]+\]: segfault" /tmp/dmesg.txt | awk -F 'in SlamTest' '{print $2}' | awk -F '[' '{print $2}' | awk -F ']' '{print $1}')
# echo $addr_begin
# 5573b4000000+28d000
# 去除+后面的值
addr_begin=$(echo $addr_begin | awk -F '+' '{print $1}')
# echo $addr_begin
# 计算出addr_begin和addr_end的十六进制差值
addr_diff=$((16#$addr_end-16#$addr_begin))
# 将差值转换成十六进制
addr_diff_hex=$(printf "%x" $addr_diff)
# echo $addr_diff_hex
addr2line -a -C -e SlamTest -f  $addr_diff_hex
```

