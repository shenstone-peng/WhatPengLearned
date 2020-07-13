# 8.Shell
## 8.1 sed
1. 基础用法总结
```bash
sed '/^#/chello' fstab    		# 把以\#开头的整行替换为hello
sed '/^#/chello\nwestos' fstab 	 # 把以\#开头的整行替换为两行
sed -ne '/^UUID/=;/^UUID/p' fstab
sed -ne '/^UUID/=;/^UUID/p' fstab | sed 'N;s/\n/ /g'

sed '=' fstab | sed 'N;s/\n/ /g'  	# 在全文前加行号
sed 's/#/ /g' fstab        		# 将全文的#替换成空格
sed '2,5s/#/ /g' fstab     		# 将2-5行的#替换成空格
sed '2s/#/ /g' fstab       		# 将第2行的#替换成空格
sed '2s/#/ /g;5s/#/ /g' fstab  	# 将第二行和第五行的#替换成空格
sed '/Created/,/See/s/#/ /g' fstab  	# 将/Create/到/See/行中的#替换成空格
sed 's@/@ @g' fstab        		# 将/替换成空格
sed 'G' fstab              		# 在每一行后面加回车

其中 s 代表行， g 代表列
```
```
#---------------------------------------
sed总结
sed元字符（正则表达式）
^ 匹配行开始，如：/^sed/匹配所有以sed开头的行。
$ 匹配行结束，如：/sed$/匹配所有以sed结尾的行。
. 匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。
* 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。
[] 匹配一个指定范围内的字符，如/[ss]ed/匹配sed和Sed。
[^] 匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。
\(..\) 匹配子串，保存匹配的字符，如s/\(love\)able/\1rs，loveable被替换成lovers。
& 保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。
\< 匹配单词的开始，如:/\ 匹配单词的结束，如/love\>/匹配包含以love结尾的单词的行。
x\{m\} 重复字符x，m次，如：/0\{5\}/匹配包含5个0的行。
x\{m,\} 重复字符x，至少m次，如：/0\{5,\}/匹配至少有5个0的行。
x\{m,n\} 重复字符x，至少m次，不多于n次，如：/0\{5,10\}/匹配5~10个0的行。

```
2. 用例
```bash
for fname in `ls $_AUTO_DEPLOY_VERSION_FROM_JENKINS_DIR/zxcdn_iptv_${VM_IP}/csv`
do newname=`echo $fname | sed "s/_.*_/_${VM_IP}_/"`
   echo $newname
   mv $_AUTO_DEPLOY_VERSION_FROM_JENKINS_DIR/zxcdn_iptv_${VM_IP}/csv/$fname $_AUTO_DEPLOY_VERSION_FROM_JENKINS_DIR/zxcdn_iptv_${VM_IP}/csv/$newname
done
修改文件名
sed -i "s/deviceip=.*/deviceip=${VM_IP}/" $_AUTO_DEPLOY_VERSION_FROM_JENKINS_DIR/zxcdn_iptv_${VM_IP}/deviceparam.ini
修改一整行l
```
3. [sed学习链接](https://blog.csdn.net/qq_38157974/article/details/78375229)

---
## 8.2 awk
1. 用例1
```bash
cat /proc/10024/smaps  | awk '/Size|Rss|Pss|Shared|Private|Referenced|Swap/{val_name=gensub(/([a-zA-Z_]*).*/,"\\1",1,$1); list[val_name]+=$2; }END{for(val in list)print val,list[val];}'
```
2. 用例2
```bash
BRIpid=`ps -ef|grep "/ZMSS/ZMSSBRIProcessd -d"|grep -vE "grep|zmsscmd"|awk '{print $2}'`
private_total=`cat /proc/${BRIpid}/smaps 2>/dev/null |awk '
BEGIN{    total_size=0;   total_rss=0;   total_shared_clean=0;   total_shared_dirty=0;   total_private_clean=0;   total_private_dirty=0;   total_swap=0;   mem_size=0;   mem_rss=0;   mem_shared_clean=0;   mem_shared_dirty=0;   mem_private_clean=0;   mem_private_dirty=0;   mem_swap=0;}
{
   if($1 ~ /^Size:/)
   {
	if($3 == "gB")
	{
       	    mem_size=$2;
	    printf("kb")
	}
	total_size=total_size+mem_size;
   }
   else if($1 ~ /^Rss:/)
   {
       mem_rss=$2;
	total_rss=total_rss+mem_rss;
   }
   else if($1 ~ /^Shared_Clean:/)
   {
       mem_shared_clean=$2;
	total_shared_clean=total_shared_clean+mem_shared_clean;
   }
   else if($1 ~ /^Shared_Dirty:/)
   {
       mem_shared_dirty=$2;
total_shared_dirty=total_shared_dirty+mem_shared_dirty;
   }
   else if($1 ~ /^Private_Clean:/)
   {
       mem_private_clean=$2;
total_private_clean=total_private_clean+mem_private_clean;
   }
   else if($1 ~ /^Private_Dirty:/)
   {
       mem_private_dirty=$2;
total_private_dirty=total_private_dirty+mem_private_dirty;
   }
   else if($1 ~ /^Swap:/)
   {
       mem_swap=$2;
total_swap=total_swap+mem_swap;
   };
   
   
   
}
END{ private_total=total_private_clean+total_private_dirty;  printf("%15d,%15d,%15d",total_private_clean,total_private_dirty,private_total);}
'`
```
