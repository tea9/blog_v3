---
title: angr ctf
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
abbrlink: 824731233
date: 2021-03-26 10:19:04
tags:
  - android安全
  - android ctf
  - angr
categories:
  - - 安全
    - 移动安全
    - android安全
---

## 安装
docker:
```
安装
docker pull angr/angr
运行
docker run -it angr/angr
后台运行
docker run -itd angr/angr
进入docker
docker exec -it -i (id前4位) /bin/bash
用su angr命令切换到angr用户
su angr
主机向docker中拷贝：
docker cp (本地文件路径) (docker container id):(容器中路径)
docker向主机中拷贝：
docker cp (docker container id):(容器中路径) (本地文件路径)
查看
docker ps -a
启动docker
docker start (container id)
```

## 使用
样本地址：https://raw.githubusercontent.com/angr/angr-doc/master/examples/b01lersctf2020_little_engine/engine

链接: https://pan.baidu.com/s/1nyQG0VUA5fsrrmB7MdLpiw  密码: 83nf

```
sudo docker run -it angr/angr 
ls
engine
(angr) angr@4ae3685f05e5:~/tea/engine$ vim xx.py
(angr) angr@4ae3685f05e5:~/tea/engine$ python xx.py
WARNING | 2021-04-07 02:31:20,813 | angr.simos.simos | stdin is constrained to 302 bytes (has_end=True). If you are only providing the first 302 bytes instead of the entire stdin, please use stdin=SimFileStream(name='stdin', content=your_first_n_bytes, has_end=False).
pctf{th3_m0d3rn_st34m_3ng1n3_w45_1nv3nt3d_1n_1698_buT_th3_b3st_0n3_in_1940}
Time elapsed: 570.9648106098175
```
参照github上的engine例子
```
#!/usr/bin/env python
# coding: utf-8
import angr
import claripy
import time

#compiled on ubuntu 18.04 system:
#https://github.com/b01lers/b01lers-ctf-2020/tree/master/rev/100_little_engine

def main():
    #setup of addresses used in program
    #addresses assume base address of
    base_addr = 0x100000

    #length of desired input is 75 as found from reversing the binary in ghidra
    #need to add 4 times this size, since the actual array is 4 times the size
    #1 extra byte for first input
    input_len = 1+75*4

    #seting up the angr project
    p = angr.Project('./engine', main_opts={'base_addr': base_addr})

    #looking at the code/binary, we can tell the input string is expected to fill 22 bytes,
    # thus the 8 byte symbolic size. Hopefully we can find the constraints the binary
    # expects during symbolic execution
    flag_chars = [claripy.BVS('flag_%d' % i, 8) for i in range(input_len)]

    #extra \n for first input, then find the flag!
    flag = claripy.Concat( *flag_chars + [claripy.BVV(b'\n')])

    # enable unicorn engine for fast efficient solving
    st = p.factory.full_init_state(
            args=['./engine'],
            add_options=angr.options.unicorn,
            stdin=flag
           )

    #constrain to non-newline bytes
    #constrain to ascii-only characters
    for k in flag_chars:
        st.solver.add(k < 0x7f)
        st.solver.add(k > 0x20)

    # Construct a SimulationManager to perform symbolic execution.
    # Step until there is nothing left to be stepped.
    sm = p.factory.simulation_manager(st)
    sm.run()

    #grab all finished states, that have the win function output in stdout
    y = []
    for x in sm.deadended:
        if b"Chugga" in x.posix.dumps(1):
            y.append(x)

    #grab the first ouptut
    valid = y[0].posix.dumps(0)

    #parse and turn into final flag
    bt = [ chr(valid[i]) for i in range(0,len(valid),2)]
    flag = ''.join(bt)[1:76]
    return flag

def test():
    assert main() == "pctf{th3_m0d3rn_st34m_3ng1n3_w45_1nv3nt3d_1n_1698_buT_th3_b3st_0n3_in_1940}"

if __name__ == "__main__":
    before = time.time()
    print(main())
    after = time.time()
    print("Time elapsed: {}".format(after - before))
```