[管理用户及权限 - 用户 - 《华为 openGauss (GaussDB) 1.0 使用手册》 - 书栈网 · BookStack](https://www.bookstack.cn/read/opengauss-1.0-zh/eb084a29c51ae9d0.md)



su - opengauss

vim /var/lib/opengauss/data/postgresql.conf

vim /var/lib/opengauss/data/pg_hba.conf

/usr/local/opengauss/bin/gs_ctl restart

gsql -d postgres -r

port

ALTER USER opengauss IDENTIFIED BY "你要设置的密码";

CREATE USER testuser IDENTIFIED BY '你要设置的密码';

ALTER USER testuser SYSADMIN;

\q

systemctl stop firewalld

systemctl disable firewalld.service

systemctl stop firewalld.service





gs_install -X /opt/software/openGauss/clusterconfig.xml --gsinit-parameter="--encoding=UTF8"  --dn-guc="max_process_memory=2GB" --dn-guc="shared_buffers=128MB" --dn-guc="bulk_write_ring_size=128MB"  --dn-guc="cstore_buffers=16MB"