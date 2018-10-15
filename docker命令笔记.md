---
title: docker
date: 2018-10-14 17:43:00
tags: [docker]
categories: [docker]

---

# docker命令
> [菜鸟API文档](http://www.runoob.com/docker/docker-command-manual.html)  
> [易百API文档](https://www.yiibai.com/docker/)  
> 还有各位大佬的博客(链接都在下面)

<!--more-->

```bash
docker
    通常 (将部分命令简化)
        events        从docker获取实时事件。  #示例:http://www.runoob.com/docker/docker-events-command.html
        images        参考image的ls
        info          显示 Docker 系统信息，包括镜像和容器数。  #示例:http://www.runoob.com/docker/docker-info-command.html
        login         登陆到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub。  #示例:http://www.runoob.com/docker/docker-login-command.html
        logout        登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub。  #示例:http://www.runoob.com/docker/docker-login-command.html
        ps            参考container的ls
        rmi           参考image的rm
        search        从Docker Hub查找镜像。  #示例:http://www.runoob.com/docker/docker-search-command.html
        version       显示docker的版本信息
    
    container (大多数可简化命令，也就是不用写container)
        attach        获得容器输出，很难关闭，慎用。  #示例:http://www.runoob.com/docker/docker-attach-command.html
        commit        根据容器创建一个镜像。  #示例:http://www.runoob.com/docker/docker-commit-command.html
        cp            用于容器与主机之间的数据拷贝。  #示例:http://www.runoob.com/docker/docker-cp-command.html
        create        创建一个新的容器但不启动它，语法同。  #示例:http://www.runoob.com/docker/docker-create-command.html
        diff          检查容器里文件结构的更改。  #示例:https://www.yiibai.com/docker/diff.html
        exec          在运行的容器中执行命令(cmd)。  #示例:http://www.runoob.com/docker/docker-exec-command.html
        export        将文件系统作为一个tar归档文件导出到STDOUT。  #示例:http://www.runoob.com/docker/docker-export-command.html
        inspect       获取容器的详细信息，当不加container时也可查看镜像的详细信息。  #示例:http://www.runoob.com/docker/docker-inspect-command.html
        kill          暴力不解释。  #示例:http://www.runoob.com/docker/docker-kill-command.html
        logs          获取容器的日志。注意和attach不同。  #示例:http://www.runoob.com/docker/docker-logs-command.html
        ls            列出所有容器。此命令不能省略container！  #示例:https://www.yiibai.com/docker/container_ls.html
        pause         暂停容器中所有的进程。  #示例:http://www.runoob.com/docker/docker-pause-unpause-command.html
        port          列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口。前面是容器端口，后面是宿主机端口。此命令不能省略container！  #示例:http://www.runoob.com/docker/docker-port-command.html
        prune         删除所有停止运行的容器，此命令不能省略container！。  #示例:https://www.yiibai.com/docker/container_prune.html
        rename        重命名一个容器。  #示例:https://www.yiibai.com/docker/plugin_rename.html
        restart       重启一个容器。  #示例:http://www.runoob.com/docker/docker-start-stop-restart-command.html
        rm            删除一个容器。  #示例:http://www.runoob.com/docker/docker-rm-command.html
        run           创建一个新的容器并运行一个命令。  #示例:http://www.runoob.com/docker/docker-run-command.html
        start         启动一个或多个已经被停止的容器。  #示例:http://www.runoob.com/docker/docker-start-stop-restart-command.html
        stats         显示容器的实时流资源使用统计信息。用ctrl+z退出。  #示例:https://www.yiibai.com/docker/container_stats.html
        stop          停止一个运行中的容器。  #示例:http://www.runoob.com/docker/docker-start-stop-restart-command.html
        top           查看容器中运行的进程信息，支持 ps 命令参数。注意与stats的区别。  #示例:http://www.runoob.com/docker/docker-top-command.html
        unpause       恢复容器中所有被暂停(pause)的进程。  #示例:http://www.runoob.com/docker/docker-pause-unpause-command.html
        update        更新一个或多个容器的配置。  #示例:https://www.yiibai.com/docker/container_update.html
        wait          阻塞一个或多个容器停止，然后打印退出代码。  #示例:https://www.yiibai.com/docker/container_wait.html
    
    config (此命令仅能在 Swarm 集群中使用，具体作用请百度)
        create        创建配置文件。
        inspect       检查一个或多个配置文件的具体信息。
        ls            列出所有的配置文件。
        rm            删除一个或多个配置文件。
        
    image (大多数可简化命令，也就是不用写image)
        build         从Docker文件构建image。  #示例:https://www.yiibai.com/docker/image_build.html
        history       显示映像的c创建过程(历史)。  #示例:https://www.yiibai.com/docker/image_history.html
        import        从tarball导入内容以创建文件系统映像。  #示例:https://www.yiibai.com/docker/image_import.html
        inspect       显示一个或多个映像的详细信息。  #示例:https://www.yiibai.com/docker/image_inspect.html
        load          从tar存档或STDIN加载映像(本地载入映像)。  #示例:https://www.yiibai.com/docker/image_load.html
        ls            列出所有映像，此命令不能省略image！  #示例:https://www.yiibai.com/docker/image_ls.html
        prune         删除未使用的映像。 如果指定了-a，还将删除任何容器未引用的所有映像，此命令不能省略image！  #示例:https://www.yiibai.com/docker/image_prune.html
        pull          从注册表中拉出映像或存储库(在线拉映像)。  #示例:https://www.yiibai.com/docker/image_pull.html
        push          将映像或存储库推送到注册表(上传映像)。  #示例:https://www.yiibai.com/docker/image_push.html
        rm            删除一个或多个映像，此命令不能省略image！。  #示例:https://www.yiibai.com/docker/image_rm.html
        save          将一个或多个映像保存到tar存档(默认情况下流式传输到STDOUT)。  #示例:https://www.yiibai.com/docker/image_save.html
        tag           创建引用SOURCE_IMAGE的标签TARGET_IMAGE。也就是给一个映像重新命名。默认保留原来的映像再重新创建一个重命名后的映像。如果想强制覆盖请使用-f开关。  #示例:https://blog.phpgao.com/docker_tag.html
    
    network (跟系统环境相关，可能会出现某些未知错误)
        connect       用于将容器连接到网络。可以按名称或ID连接容器。 一旦连接，容器可以与同一网络中的其他容器通信。  #示例:https://www.yiibai.com/docker/network_connect.html
        create        用于创建一个新的网络连接。 DRIVER接受内置网络驱动程序的桥接或覆盖。如果安装了第三方或自己的自定义网络驱动程序，则可以在此处指定DRIVER。 如果不指定--driver选项，该命令将为您自动创建一个桥接网络。 当安装Docker Engine时，会自动创建桥接网络。 该网络对应于Engine传统依赖的docker0网桥。 当启动使用docker run运行新容器时，它将自动连接到此桥接网络。不能删除此默认网桥，但可以使用network create命令创建新的网络。  #示例:https://www.yiibai.com/docker/network_create.html
        disconnect    断开容器的网络。容器必须运行才能将其与网络断开连接。  #示例:https://www.yiibai.com/docker/network_disconnect.html
        inspect       显示一个或多个网络的详细信息。它返回有关一个或多个网络的信息。 默认情况下，此命令将所有结果呈现在JSON对象中。  #示例:https://www.yiibai.com/docker/network_inspect.html
        ls            用于列出网络。列出引擎守护进程知道的所有网络。 这包括跨群集中多个主机的网络。  #示例:https://www.yiibai.com/docker/network_ls.html
        prune         删除所有未使用的网络。未使用的网络是不被任何容器引用的网络。  #示例:https://www.yiibai.com/docker/network_prune.html
        rm            删除一个或多个网络。按名称或标识符删除一个或多个网络。 要删除网络，必须要先断开连接到它的任何容器的网络。  #示例:https://www.yiibai.com/docker/network_rm.html
    
    node (swarm集群管理器节点)
        demote        从群集管理器中降低(剔除)一个或多个节点。此命令针对作为群集管理器的Docker引擎。  #示例:https://www.yiibai.com/docker/node_demote.html
        inspect       显示一个或多个节点的详细信息。 默认情况下，此命令将所有结果呈现在JSON数组中。可以指定为每个结果执行给定模板的格式。 Go的文本/模板包描述了格式的所有细节。  #示例:https://www.yiibai.com/docker/node_inspect.html
        ls            列出群组中的节点。列出Docker Swarm管理器知道的所有节点。 可以使用-f或--filter标志进行过滤。有关可用过滤器选项的更多信息，请参阅过滤部分。  #示例:https://www.yiibai.com/docker/node_ls.html
        promote       将一个或多个节点推入到群集管理器中。此命令针对作为群集管理器的docker引擎。  #示例:https://www.yiibai.com/docker/node_promote.html
        ps            列出在一个或多个节点上运行的任务，默认为当前节点。  #示例:https://www.yiibai.com/docker/node_ps.html
        rm            从群集中删除一个或多个节点。从管理器节点运行时，从群集中删除指定的节点。  #示例:https://www.yiibai.com/docker/node_rm.html
        update        更新一个节点。更新关于节点的元数据，例如其可用性，标签或角色。  #示例:https://www.yiibai.com/docker/node_update.html
    
    plugin (插件)
        create        从rootfs和配置创建一个插件。插件数据目录必须包含config.json和rootfs目录。  #示例:https://www.yiibai.com/docker/plugin_create.html
        disable       禁用插件。插件必须先安装才能被禁用，请参阅docker plugin install。如果不指定-f选项，不能禁用具有引用(例如卷，网络)的插件。  #示例:https://www.yiibai.com/docker/plugin_disable.html
        enable        启用插件。插件必须先安装才能启用，请参阅docker plugin install。  #示例:https://www.yiibai.com/docker/plugin_enable.html
        inspect       显示一个或多个插件的详细信息。默认情况下，此命令将所有结果呈现为JSON数组。  #示例:https://www.yiibai.com/docker/plugin_inspect.html
        install       安装一个插件。Docker首先查看Docker主机上的插件。 如果插件在本地不存在，那么该插件是从注册表中提取的。请注意，分发插件的最小注册表版本为2.3.0。  #示例:https://www.yiibai.com/docker/plugin_install.html
        ls            列出所有安装的插件。  #示例:没有  #示例。
        push          将插件推送到注册表。  #示例:没有  #示例
        rm            删除一个或多个插件。如果启用了插件，则无法删除插件，必须在删除插件之前禁用Docker插件(或使用-force，但不推荐使用强制删除)，因为它可能会影响使用插件运行容器的功能。  #示例:https://www.yiibai.com/docker/plugin_rm.html
        set           更改插件的设置。但插件必须先禁用了才能修改设置。  #示例:https://www.yiibai.com/docker/plugin_set.html
        upgrade       升级现有插件。将现有插件升级到指定的远程插件映像。 如果没有指定远程，Docker将重新拉动当前映像并使用更新的版本。 所有现有对插件的引用将继续工作。 在运行升级之前必须禁用插件。  #示例:https://www.yiibai.com/docker/plugin_upgrade.html
        
    secret (容器安全相关)
        create        从一个文件或者标准输入流中创建secret
        inspect       显示一个或多个secret的详细信息
        ls            列出所有secret
        rm            删除一个或多个secret
        
    service
        create        创建服务。按照指定参数的描述创建一个服务。必须在管理器节点上运行此命令。  #示例:https://www.yiibai.com/docker/service_create.html
        inspect       显示有关一个或多个服务的详细信息。 必须以管理员节点为目标运行此命令。默认情况下，这将使JSON数组中的所有结果呈现。如果指定了格式，则将为每个结果执行给定的模板。  #示例:https://www.yiibai.com/docker/service_inspect.html
        logs          获取服务的日志。docker服务记录命令批量检索执行时存在的日志。注意：此命令仅适用于使用json-file或日志记录驱动程序启动的服务。  #示例:https://www.yiibai.com/docker/service_logs.html
        ls            列出服务，此命令在运行定向管理器时，正在群集中运行的列表服务。  #示例:https://www.yiibai.com/docker/service_ls.html
        ps            列出服务的任务。列出作为指定服务一部分运行的任务。 必须以管理员节点为目标运行此命令。  #示例:https://www.yiibai.com/docker/service_ps.html
        rm            删除一个或多个服务。从群集中删除指定的服务，必须以管理员节点为目标运行此命令。  #示例:https://www.yiibai.com/docker/service_rm.html
        rollback      服务更新失败后回滚服务。  #示例https://yq.aliyun.com/articles/71039
        scale         将一个或多个复制服务向上或向下缩放到所需数量的副本。 此命令不能应用于全局模式的服务。 该命令将立即返回，但服务的实际缩放可能需要一些时间。 要停止服务的所有副本，同时保持服务在群集中的活动，您可以将比例设置为0。  #示例:https://www.yiibai.com/docker/service_scale.html
        update        更新服务。它根据指定的参数更新服务。 必须以管理员节点为目标运行此命令。参数与docker service create相同。请查看有关进一步信息的描述。  #示例:https://www.yiibai.com/docker/service_update.html
    
    system (查看docker的系统方面参数以及管理，请参考:http://www.hangdaowangluo.com/archives/2148)
        df            整体磁盘的使用情况
        events        获取docker系统实时事件，不包括容器内的。
        info          查看整个docker系统的信息
        prune         清理资源，此操作尤其需要注意。
    
    vloume (数据卷，平时使用的以临时挂载多，也就是开关-v，具体参考:https://yeasy.gitbooks.io/docker_practice/data_management/volume.html)
        create        创建一个数据卷
        inspect       显示一个数据卷的详细信息
        ls            列出docker中所有定义的数据卷(不显示临时数据卷)
        prune         清理所有没有被引用的数据卷。
        rm            删除一个或多个数据卷
    
```
# docker其他命令
```bash
#启动docker
sudo service docker start
sudo systemctl start docker

#创建容器
Docker run --name 容器名 -v 系统挂载目录:docker内部目录 -e 环境变量设置 -p 端口映射 镜像名。
-v 开关用于将docker中的某个目录挂载到docker之外的系统目录中，当docker内部访问到该目录时会重定向到外部的系统目录。可设置多个-v
-e 开关声明环境变量
-p 主机端口：docker内部端口
-d (后台启动开关)
例 -> docker run --name mysql -d -v /Users/helloworld/DockerProjects/mysql/data:/var/lib/mysql -p3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7 

容器的外部路径
Docker就是虚拟机，其文件结构作为主机的一部分存储在某个地方，我们能在不启动docker的情况下进入docker内部编辑文件。目前遇到的docker存放在/var/lib/docker/overlay2目录下。注意：文件名和docker id不一定相同，建议使用find命令定位文件。
docker inspect后在其中找到‘UpperDir’字段，其后面跟着的路径在linux系统下是此容器的物理存储地址，但在神奇的mac下很难找容器真正的外部路径

容器中访问宿主机端口（以mysql的3306为例）
Mac系统：Docker内的网络和宿主机的网络设置在内部的局域网内，在18.03和其之后的版本中用host.docker.internal代表宿主机ip地址，gateway.docker.internal代表此局域网的网关。sql连接的url则就写成了jdbc:mysql://host.docker.internal:3306/lakers.
Linux系统：默认使用一个叫docker0的网关连接docker和宿主机。在宿主机中输入ip addr show docker0 可查看在docker0网关中的地址（默认应该是172.17.0.1的路由器地址）在容器中则可以使用ip route show查看宿主机的ip以及此容器的ip

```

