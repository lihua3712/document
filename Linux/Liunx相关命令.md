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