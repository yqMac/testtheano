# 修改后的theano验证码识别   
### 项目基于https://github.com/yqMac/Captcha-Decoder   
### theano及其他依赖库只能使用旧版，切记注意    
### 该项目主要是使用了Lasagne框架，是该框架的简单实例   
### 对该源码的详细理解可参考：http://www.jianshu.com/p/e10c3b5a278f   

更新apt-get
apt-get update   
  

GCC  
apt-get install build-essential  


# python2.7
1、下载官方源码:   
https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz  
或https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tar.xz  
2、解压后进入目录Python-2.7.13；  
3、配置并编译  
./configure —prefix=/usr/local/  CFLAGS=-fPIC  
防止zlib问题  
yum install zlib-devel 或者 apt-get install alib1g-dev  
进入 Python安装包,修改Module路径的setup文件    
vimmodule/setup    
#zlibzlibmodule.c-I$(prefix)/include-L$(exec_prefix)/lib-lz  
去掉注释   
zlib zlibmodule.c-I$(prefix)/include-L$(exec\_prefix)/lib-lz   
make   
make install  
4、测试安装成功  
输入python，能进入python环境则成功  
  http://www.jianshu.com/p/7d7c5cf267f4
  
  
# 安装pip  
wget https://bootstrap.pypa.io/get-pip.py   
python get-pip.py  

### 修复yum  
export PATH=$PATH:/usr/local/lib/python2.7/dist-packages  
pip easy_install 软连接。  
# 安装依赖库：  
pip -V  
pip install numpy  
pip install scipy  
pip install nose  
pip install theano==0.8.2  
pip install Lasagne==0.1  
pip install pydot==1.1.0  
pip install Pillow  
pip install watchdog  
pip install observers  

  
# 生成训练集
进入training_data_gen 目录  
运行python training_data\_gen.py image\_dir max\_size length -case\_sensitive=False -captcha\_pattern='^\d+\_(.*)\\..+$'  
可以根据train\_parse\_args文件查看详细含义：  
  * image\_dir为图片存储目录  
  * max\_size为多少张图片一个训练集文件  
  * length为一张图片的字符数量  
  * case\_sensitive为是否字符大小写敏感 默认False
  * captcha\_pattern为图片的名字的正则，group1为正确标签

### 训练的结果默认会存在一个工程目录下uuid目录下 ###

# 训练model
进入model目录  
运行python  captcha_cracker.py TrainingModeId img\_height img\_width length -ResultPre  
可以根据parse\_arg 文件查看配置详细含义或者更详细配置  
  * TrainingModeId 为生成训练集时位置的uuid  
  * img\_height 图片高度  
  * img\_width 图片宽度  
  * length 验证码字符数量  
  * ResultPre 生成的model的前缀，默认为lstm_  
### 最终结果会存储在uuid目录下得result目录下，并跟随训练进度不断更新。当训练成功率满意以后即可停止训练。
### 后台运行
nohup python captcha_cracker.py 4c39c9bc6dc511e7a5b91c1b0d56a143 40 80 4 >nohup.out 2>&1 &  
### 查看任务  
jobs -l  
关闭直接kill  


# 搭建识别环境
### rest-server v1.0
 * 将训练结果集以lstm_(.*?)\_{height}\_{width}\_{site}\_{num}\_npy.npz文件名存放于rest_server目录下lstm目录中

   例如:  
   ① lstm\_ydsn\_170614\_40\_80\_10003\_4_npy.npz  
 * name为名称，site为自定义的随机标示，num为验证码字符数量
 上例中ydsn\_170614为自定义名称，10003为site，4为字符数量
 * 运行 python server.py  默认端口为8088

### rest-server v2.0  
 * 将训练结果集以 rookie\_{site}(_.\*?)?\_npy.npz文件名存放于rest_server目录下lstm目录中  

   例如 :  
   ① rookie\_171029\_captchaname\_npy.npz  
   ② rookie\_171029\_npy.npz  
 * site为自定义的标示  
 上例中_captchaname为自定义名称，171029为site  
 * 复写项目根目录下的config中的各种配置项  
 * 运行 python server.py  

### 后台运行  
v1.0 nohup python server.py >nohup.out 2>&1 &  
v2.0 nohup python server.py &  
### 查看任务  
jobs -l
关闭直接kill

# 支持的Http请求  
* 验证码识别  
POST /pyocr  
{  
    "site":10003,  
    "image":"base64的图片"  
}  

* 返回当前信息  
GET /list  

其中site为搭建环境中自定义标示site
image为验证码图片的base64位编码  
### 注意要去掉图片编码后的base64前缀调用
  
# GPU 搭建

  
  