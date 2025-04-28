安装wsl1, 交叉编译
1. 安装wsl
    wsl --import u20 D:\wsl\env\u20 D:\wsl\pkg\u20.tar --version 1
2. 安装ros
    wget http://fishros.com/install -O fishros && sudo bash fishros
3. 安装交叉编译链
    apt install g++-aarch64-linux-gnu gcc-aarch64-linux-gnu 
4. 改变ros位置
    mv /opt/ros/ /opt/ros_pc
    ln -s /opt/ros_pc/ /opt/ros
5. 更改交叉编译lib位置
    ln -s /mnt/d/ws/test/cross_compiler/cross_compile/usr/lib/aarch64-linux-gnu /usr/lib/aarch64-linux-gnu
6. 添加git密码记录
    git config --global credential.helper store
7. 处理rviz问题
    strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5