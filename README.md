## 基于GitAction和image-syncer实现镜像同步

国内拉取镜像的时候，由于特殊原因无法拉取到国外镜像；通过GitAction和image-syncer，将国外镜像仓库同步到阿里云镜像仓库，当然你也可以同步到其他镜像仓库

### 配置文件

####  auth.json  镜像仓库认证配置
```json
{
    "registry.cn-hangzhou.aliyuncs.com": {
        "username": "${ALIYUN_REGISTRY_USERNAME}",
        "password": "${ALIYUN_REGISTRY_PASSWORD}"
    },
    "registry.hub.docker.com": {
        "username": "${DOCKERHUB_USERNAME}",
        "password": "${DOCKERHUB_USERNAME}"
    }
}
```

#### images.json  需要同步的镜像仓库列表

```json
{
    "k8s.gcr.io/descheduler/descheduler:v0.25.1": "registry.cn-hangzhou.aliyuncs.com/seam/descheduler:v0.25.1"
}
```

可以将以上两个配置文件合并成一个，为了方便区分此处将配置拆分认证文件和镜像同步列表
#### config.json
```json
{
    "auth": {
        "registry.hub.docker.com": {
            "username": "DOCKERHUB_USERNAME",
            "password": "DOCKERHUB_PASSWORD"
        }
    },
    "images": {
        "k8s.gcr.io/ingress-nginx/controller": "registry.cn-hangzhou.aliyuncs.com/seam/ingress-nginx-controller"
    }
}
```

### 设置仓库用户名和密码
![](/images/secrets.png)


### 执行同步
```bash
./image-syncer --proc=2 --auth=./auth.json --images=./images.json --namespace=seam --retries=2
# or
./image-syncer --proc=2 --config=config.json --namespace=seam --retries=2
```

### 参考资料
[image-syncer](https://github.com/AliyunContainerService/image-syncer.git)