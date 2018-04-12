# EB 佈署 Docker

https://docs.aws.amazon.com/zh_cn/elasticbeanstalk/latest/dg/create_deploy_docker.html

## Single Container

文件這篇最核心

https://docs.aws.amazon.com/zh_cn/elasticbeanstalk/latest/dg/create_deploy_docker_image.html

### 創建 Docker 相關檔案

1. (Required) 创建 Dockerrun.aws.json 文件以将 Docker 容器从现有 Docker 映像部署到 Elastic Beanstalk。
2. (Optional) 创建 Dockerfile 以自定义映像并将 Docker 容器部署到 Elastic Beanstalk。
3. (Optional) 创建一个包含应用程序文件、应用程序文件依赖项、Dockerfile 以及 Dockerrun.aws.json 文件的 .zip 文件。
   如果您只用一个 Dockerfile 或只用一个 Dockerrun.aws.json 文件部署应用程序，则无需将文件压缩为一个 .zip 文件。

### Dockerrun.aws.json 與 Dockerfile

> 您可以只为 Elastic Beanstalk 提供 Dockerrun.aws.json 文件，或为其提供同时包含 Dockerrun.aws.json 和 Dockerfile 文件的 .zip 存档。在提供这两个文件时，Dockerfile 描述 Docker 映像，Dockerrun.aws.json 文件提供其他部署信息，如本节后面所述。
    
**NOTE**
> 这两个文件必须位于 .zip 存档的根级或顶级。请不要从包含这些文件的目录生成存档。导航到该目录并在其中生成存档。
    
### image

> 指定现有 Docker 存储库上的 Docker 基本映像，您将从其构建 Docker 容器。指定 Name 键的值：对于 Docker Hub 上的映像，采用 `<organization>/<image name>` 的格式；对于其他站点，采用 `<site>/<organization name>/<image name>` 的格式。

這點跟 ECS 不一樣，ECS 的 Container image 如果是抓 Docker hub 的話，因為是預設的，所以可以不用加 <organization>`
    
但是我試過抓 Docker Hub 上的 nginx，也可以不用寫 `<organization>`


**NOTE**
> 如果同時提供了 Dockerrun.aws.json 和 Dockerfile，请不要在 Dockerrun.aws.json 文件中指定映像。Elastic Beanstalk 生成和使用 Dockerfile 中描述的映像，并忽略 Dockerrun.aws.json 文件中指定的映像。

**ElasticBeasntalk 優先採用 Dockerfile 的 image**


### Deploy a simple nginx server
```json
{
  "AWSEBDockerrunVersion": "1",
  "Image": {
    "Name": "nginx",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": "80"
    }
  ],
  "Volumes": [
    {
      "HostDirectory": "/var/app/mydb",
      "ContainerDirectory": "/etc/mysql"
    }
  ],
  "Logging": "/var/log/nginx"
}
```
(已測試過可以正確 deploy 並連線到 URL 得到 nginx default page)
(還沒成功連入 container)

### With custom Dockerfile

TODO..

## Multicontainer

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html

> Elastic Beanstalk 使用 Amazon Elastic Container Service (Amazon ECS) 协调到多容器 Docker 环境的部署。
> - Amazon ECS 提供工具来管理运行 Docker 容器的实例群集。
> - Elastic Beanstalk 处理 Amazon ECS 任务，包括群集创建、任务定义和执行。

### containerDefinition 中的 image

> image
> 您从中构建 Docker 容器的在线 Docker 存储库中的 Docker 映像的名称。记下这些约定：
>   - Docker Hub 上的官方存储库中的映像使用一个名称 (例如，ubuntu 或 mongo, nginx)。
>   - Docker Hub 上其他存储库中的映像通过组织名称 (例如，amazon/amazon-ecs-agent) 进行限定。
>   - 其他在线存储库中的映像由域名 (例如，quay.io/assemblyline/ubuntu) 进行进一步限定。

這倒是結合了 v1 和 ECS

### Dockerrun.aws.json v1 vs v2

> 版本 1 的 Dockerrun.aws.json 格式用于对 Elastic Beanstalk 环境启动单个 Docker 容器。版本 2 添加了对每个 Amazon EC2 实例对应多个容器的支持，并且只能与多容器 Docker 平台一起使用。格式与 单容器 Docker 配置 下详细介绍的上一个版本存在很大的差异

NOTE: 發現大部分的專案都是用 v2，鮮少看到用 v1 的

### 不支援自定義 Dockerfile
> Elastic Beanstalk 上的多容器 Docker 平台不支持使用 Dockerfile 在部署期间构建自定义映像。

### Deploy a simple Nginx with Node.js 

    
TODO...

