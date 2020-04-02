## 准备工作 ##
#### 安装`virtualenv`
安装这个东东是主要目的，是防止与其他项目的依赖包发生版本冲突  
``` shell
 pip install virtualenv -i https://mirrors.aliyun.com/pypi/simple
```
> 安装过程中曾经踫到依赖包`filelock`更新出错，系统表示无法卸载老的版本，也就无法安装新的版本，进而导致`virtualenv`安装失败，解决办法：   
> ``` shell
> pip install filelock --upgrade --ingore-installed filelock
> ```

#### 创建虚拟环境
``` shell
mkdir djangoProjects
cd djangoProjects
virtualenv django
```

### 安装`django`
```
pip install django -i https://mirrors.aliyun.com/pypi/simple
```

## 创建项目
