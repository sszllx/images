# gitlab安装说明
1. 拉取gitlab-ee镜像
```bash
docker pull gitlab/gitlab-ee:latest
```
2. 安装镜像
```bash
sudo docker run --detach \
  --hostname 101.133.142.164 \
  --publish 8443:443 --publish 8037:80 --publish 8022:22 \
  --name gitlab \
  --restart always \
  --volume /srv/gitlab/config:/etc/gitlab \
  --volume /srv/gitlab/logs:/var/log/gitlab \
  --volume /srv/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ee:latest
```
3. 访问http://101.133.142.164:8037 测试
4. 备份数据
```bash
docker exec -t <container name> gitlab-backup create
```
```bash
root@iZu:/mnt/work# docker exec -t gitlab gitlab-backup create
2020-01-13 03:33:56 +0000 -- Dumping database ...
Dumping PostgreSQL database gitlabhq_production ... [DONE]
2020-01-13 03:33:57 +0000 -- done
2020-01-13 03:33:57 +0000 -- Dumping repositories ...
2020-01-13 03:33:57 +0000 -- done
2020-01-13 03:33:57 +0000 -- Dumping uploads ...
2020-01-13 03:33:57 +0000 -- done
2020-01-13 03:33:57 +0000 -- Dumping builds ...
2020-01-13 03:33:57 +0000 -- done
2020-01-13 03:33:57 +0000 -- Dumping artifacts ...
2020-01-13 03:33:57 +0000 -- done
2020-01-13 03:33:57 +0000 -- Dumping pages ...
2020-01-13 03:33:57 +0000 -- done
2020-01-13 03:33:57 +0000 -- Dumping lfs objects ...
2020-01-13 03:33:57 +0000 -- done
2020-01-13 03:33:57 +0000 -- Dumping container registry images ...
2020-01-13 03:33:57 +0000 -- [DISABLED]
Creating backup archive: 1578886437_2020_01_13_12.6.3-ee_gitlab_backup.tar ... done
Uploading backup archive to remote storage  ... skipped
Deleting tmp directories ... done
done
done
done
done
done
done
done
Deleting old backups ... skipping
Warning: Your gitlab.rb and gitlab-secrets.json files contain sensitive data
and are not included in this backup. You will need these files to restore a backup.
Please back them up manually.
Backup task is done.
```
```bash
root@iZu:/srv/gitlab/data/backups# ls
1578886437_2020_01_13_12.6.3-ee_gitlab_backup.tar
```
5. 恢复数据
```bash
docker exec -it <container name> gitlab-backup restore
```
6. 备份配置
```bash
docker exec -t gitlab /bin/sh -c 'umask 0077; tar cfz /etc/gitlab/config_backup/$(date "+etc-gitlab-%s.tgz") -C / etc/gitlab'
```
7. 配置定时任务
```bash
10 01 * * *  docker exec -t gitlab gitlab-backup create  && cd /srv/gitlab/data/backups && cp $(ls -t | head -n1) /mnt/work/gitlab-backup/
20 01 * * *  docker exec -t gitlab /bin/sh -c 'umask 0077; tar cfz /etc/gitlab/config_backup/$(date "+etc-gitlab-\%s.tgz") -C / etc/gitlab' && cd /srv/gitlab/config/config_backup && cp $(ls -t | head -n1) /mnt/work/gitlab-backup/

```
