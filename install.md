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
4. 备份
```bash
docker exec -t <container name> gitlab-backup create
```
5. 恢复
```bash
docker exec -it <container name> gitlab-backup restore
```
