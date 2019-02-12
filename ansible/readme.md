added option to dockerd in /etc/sysconfig/docker: OPTIONS="--enable-secrets=false (this secrets thing is a bug in RH Docker)
chmod a+rwx /var/log/large_scale - because nova fake had some problem around accessing the logs
