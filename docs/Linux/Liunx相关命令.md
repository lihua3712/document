1、uname －a ?

Linux查看版本当前操作系统内核信息??

2、cat /proc/version

Linux查看当前操作系统版本信息? ? ??

3、 cat /etc/issue ?或cat /etc/redhat-release

Linux查看版本当前操作系统发行版信息

4、cat /proc/cpuinfo

查看cpu的信息，

processor ? ? 逻辑处理器的id。

physical id ? ?正式的物理处理器的id。

core id? ? ? ? ? ?cpu里面的每个核心的id。

cpu cores ? ? 每个cpu物理处理器中的内核数量。

siblings ? ? ? ? 每个cpu物理处理器中的逻辑处理器的数量。


修改yum源，在安装更新rpm包时获得比较理想的速度。国内比较快的有163源、sohu源。这里以163源为例子。
cd /etc/yum.repos.d
mv CentOS-Base.repo CentOS-Base.repo.backup
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
mv CentOS6-Base-163.repo CentOS-Base.repo
yum clean all

没有已启用的仓库。 执行 "yum repolist all" 查看您拥有的仓库。
您可以用 yum-config-manager --enable &lt;仓库名&gt; 来启用仓库

yum安装netstat:
yum install net-tools


开放8777端口供外网访问:
如果是云服务器，开放安全组即可，如果是虚拟机在防火墙中加入8777端口并重载配置

        # 开启防火墙8777端口
        $ firewall-cmd --zone=public --add-port=8777/tcp --permanent
        
        # 使配置生效
        $ firewall-cmd --reload
        
        # 防火墙扩展命令如下（云服务直接操作阿里云安全组即可）
        （1）设置开机启用防火墙：systemctl enable firewalld.service
        （2）设置开机禁用防火墙：systemctl disable firewalld.service
        （3）启动防火墙：systemctl start firewalld
        （4）关闭防火墙：systemctl stop firewalld
        （5）检查防火墙状态：systemctl status firewalld 
        
        （1）查看防火墙状态：firewall-cmd --state
        （2）重新加载配置：firewall-cmd --reload
        （3）查看开放的端口：firewall-cmd --list-ports
        （4）开启防火墙端口：firewall-cmd --zone=public --add-port=9200/tcp --permanent
        　　命令含义：
        　　–zone #作用域
        　　–add-port=9200/tcp #添加端口，格式为：端口/通讯协议
        　　–permanent #永久生效，没有此参数重启后失效
        　　注意：添加端口后，必须用命令firewall-cmd --reload重新加载一遍才会生效
        （5）关闭防火墙端口：firewall-cmd --zone=public --remove-port=9200/tcp --permanent

 
