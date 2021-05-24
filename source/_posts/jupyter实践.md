---
title: jupyter 实践
top: false
cover: false
toc: true
mathjax: true
tags:
  - jupyter
categories:
  - jupyter
top: false
date: 2020-12-31 15:51:46
---

# jupyter

> 环境：Linux

- ### 安装

    - ##### 通过Anaconda进行安装

        安装Anaconda后，会自动帮我们安装jupyter Notebook以及其它工具，还包含许多科学包及依赖。可以不用安装python解释器。次方法安装体积会比较大占用内存会比较高。

    - ##### 通过pip进行安装

        ```bash
        pip3 install jupyter
        
        # 启动  后面可以加路径
        jupyter-notebook 
        ```

        

- ### 配置虚拟环境

    创建一个虚拟环境专门给 jupyter-note 使用，避免污染系统环境 。使用`jupyter-kernelspec list`可以查看jupyter的内核

```shell
lei@xxml:~/Documents$ jupyter-kernelspec list
Available kernels:
  jupyter-venv    /home/lei/.local/share/jupyter/kernels/jupyter-venv
  python3         /home/lei/.local/share/jupyter/kernels/python
  
  
# 创建一个python的虚拟环境
virtualenv jupyter-venv

# 激活虚拟环境 并安装 jupyter内核
source  jupyter-venv/bin/activate
pip install ipykernel

# 按装完成后，运行以下代码，将虚拟环境添加到jupyter notebook中：
python -m ipykernel install --user --name (环境的名称（N）) --display-name (在jupyter中显示该环境时的环境名称)


# 至此，该虚拟环境已经安装到了jupyter notebook中了
# 打开jupyter notebook 点击新建，你会发现不仅仅有个python3，会有其他的虚拟环境选项。
# 注：通过一下指令可以将jupyter notebook 中的虚拟环境删除。
jupyter kernelspec remove (环境的名称(N))
```

- ### 配置密码

    给notebook 服务器配置密码

    - **自动生成**

        **从** **`notebook`** **5.3 版本开始**，当第一次通过令牌(token)登录的时候，`notebook`服务器会让用户有机会在用户界面设置一个密码，这将通过一个表单来询问当前的令牌以及新的密码，输入并点击 `Login and setup new password` 。

    - **手动生成**
    
        ```python
        In [1]: from notebook.auth import passwd                                  
        In [2]: passwd()                                                                
        Enter password: 
        Verify password: 
        Out[2]: 'argon2:$argon2id$v=19$m=10240,t=10,p=8$eg+cDHHfyTDz8mxrSXY7MQ$rkgYE3jtcw8F5hRoBp0Cwg'
        
        ```
    
        
    
    `passwd()` 方法在没有传入参数时候，会如上所示提示输入和验证密码，它也可以传入一个字符串作为密码，即 `passwd('mypassword')` ，但不建议这种做法，因为本来输入命令都会被保存起来，直接输入密码，相当于密码以明文方式保存在输入历史。
    
    - #### **添加到配置文件**
    
        最后一步，就是需要将哈希密码添加到配置文件 `jupyter_notebook_config.py`
    
        ```python
        c.NotebookApp.ip = '*' #所有绑定服务器的IP都能访问，若想只在特定ip访问，输入ip地址即可
        # c.NotebookApp.port = 6666 #将端口设置为自己喜欢的吧，默认是8888
        c.NotebookApp.open_browser = False #我们并不想在服务器上直接打开Jupyter Notebook，所以设置成False
        # c.NotebookApp.notebook_dir = '/root/jupyter_projects' #这里是设置Jupyter的根目录，若不设置将默认root的根目录，不安全
        c.NotebookApp.allow_root = True # 为了安全，Jupyter默认不允许以root权限启动jupyter 
        
        c.NotebookApp.password = u'argon2:$argon2id$v=19$m=10240,t=10,p=8$eg+cDHHfyTDz8mxrSXY7MQ$rkgYE3jtcw8F5hRoBp0Cwg'
        ```
    
        

