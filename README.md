# ipChangeMonitoring主机IP监控
通过python脚本定时监控检查主机公网IP地址是否发生改变，若发生改变，则通过邮件方式发送通知。

# 使用方式
## 文件下载到本地目录下
[下载地址](https://github.com/ErickQian/ipChangeMonitoring/archive/refs/heads/master.zip)  
下载解压到最终运行目录，如`/opt/ipChangeMonitoring/`下；
## 修改自定义参数
修改`send_email`函数对应内容
```python
def send_email(new_public_ip, old_public_ip):
    """发送邮件"""
    mail_host = 'smtp.qq.com'  # 邮箱服务器
    mail_user = '******@qq.com'  # 用户名
    mail_auth = '******'  # 授权码
    sender = '******@qq.com'  # 邮件发送方邮箱
    # 邮件接收方邮箱地址，可以写多个邮件地址进行群发
    receivers = ['******@qq.com']
    # 设置email信息：邮件内容设置
    new_public_ip = "The new IP address is："+new_public_ip+'\r'
    old_public_ip = "The old IP address is："+old_public_ip
    content = new_public_ip+old_public_ip
    message = MIMEText(content, 'plain', 'utf-8')
    message['Subject'] = '[warning]ip address has been changed'  # 邮件头
    message['From'] = sender  # 发送人
    message['To'] = receivers[0]  # 收件人
```  

修改`save_public_ip`和`read_old_public_ip`函数对应内容
```python
def save_public_ip():
    """
    获取公网地址
    保存到文件
    """
    # 实时获取公网IP
    public_ip = load(urlopen('https://ipv6.jsonip.com'))['ip']
    # 实际使用时填写根目录"/opt/ipChangeMonitoring/ipChangeMonitoring.log"
    file = open("/opt/ipChangeMonitoring/ipChangeMonitoring.log", 'w')
    file.write(public_ip)
    file.close()
def read_old_public_ip():
    """
    读取上一次执行获取到的公网IP
    """
    # 实际使用时填写根目录"/opt/ipChangeMonitoring/ipChangeMonitoring.log"
    filename = '/opt/ipChangeMonitoring/ipChangeMonitoring.log'
    old_public_ip = linecache.getline(filename, 1)
    return (old_public_ip)
```
## linux创建定时任务
将`ipChangeMonitoring.service`和`ipChangeMonitoring.timer`复制到`/etc/systemd/system/`目录下，并执行`systemctl daemon-reload`;  
启动定时器`systemctl start ipChangeMonitoring.timer`，设置为开机自启`systemctl enable ipChangeMonitoring.timer`。

# LICENSE