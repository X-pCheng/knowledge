```bash
# 启动服务
systemctl start vsftpd.service

# 关闭服务
systemctl stop vsftpd.service

# 重启服务
systemctl restart vsftpd.service

# 显示服务的状态
systemctl status vsftpd.service

# 在开机时启用服务
systemctl enable vsftpd.service

# 在开机时禁用服务
systemctl disable vsftpd.servic

# 查看服务是否开机启动
systemctl is-enabled vsftpd.service

# 查看已启动的服务列表
systemctl list-unit-files | grep enabled 

#或者 
systemctl list-units --type=service

# 查看启动失败的服务列表
systemctl --failed

```