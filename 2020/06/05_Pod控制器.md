## 1. Pod

### 1.1. Pod介绍

#### 1.1.1. Pod简介

Pod 是 Kubernetes 的基本构建块，它是 Kubernetes 对象模型中创建或部署的最小和最简单的单元。 Pod 表示集群上正在运行的进程。Pod 封装了应用程序容器（或者在某些情况下封装多个容器）、存储资源、唯一网络 IP 以及控制容器应该如何运行的选项。 Pod 表示部署单元：*Kubernetes 中应用程序的单个实例*，它可能由单个容器或少量紧密耦合并共享资源的容器组成。

一个pod内部一般仅运行一个pod，也可以运行多个pod，如果存在多个pod时，其中一个为主容器，其它作为辅助容器，也被称为边车模式。同一个pod共享一个网络名称空间和外部存储卷。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/378176/1579339190487-a1f2c303-a092-4f16-a7e2-2bba306fe3d2.png)

#### 

#### 1.1.2. Pod生命周期

Pod的生命周期中可以经历多个阶段，在一个Pod中在主容器(Main Container)启动前可以由init container来完成一些初始化操作。初始化完毕后，init Container 退出，Main Container启动。

在主容器启动后可以执行一些特定的指令，称为启动后钩子(PostStart)，在主容器退出前也可以执行一些特殊指令完成清理工作，称为结束前钩子(PreStop)。

在主容器工作周期内，并不是刚创建就能对外提供服务，容器内部可能需要加载相关配置，因此可以使用特定命令确定容器是否就绪，称为就绪性检测(ReadinessProbe)，完成就绪性检测才能成为Ready状态。

主容器对外提供服务后，可能出现意外导致容器异常，虽然此时容器仍在运行，但是不具备对外提供业务的能力，因此需要对其做存活性探测(LivenessProbe)。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/378176/1579445077801-c30c2449-41d5-43e6-9892-025fbcf78aaf.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_10%2Ctext_TGludXgt5rih5rih6bif%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)

#### 1.1.3. Pod状态

- Pending: Pod 已被 Kubernetes 系统接受，但有一个或者多个容器尚未创建。
- Running: 该 Pod 已经绑定到了一个节点上，Pod 中所有的容器都已被创建。至少有一个容器正在运行，或者正处于启动或重启状态。
- Succeeded: Pod 中的所有容器都被成功终止，并且不会再重启。
- Failed: Pod 中的所有容器都已终止了，并且至少有一个容器是因为失败终止。
- Unknown: 因为某些原因无法取得 Pod 的状态，通常是因为与 Pod 所在主机通信失败。

![img](https://cdn.nlark.com/yuque/0/2020/jpeg/378176/1579446234452-ef9fedb8-e923-4aef-a8c3-6862d4729009.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_14%2Ctext_TGludXgt5rih5rih6bif%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1500)

### 

### 1.2. Pod清单

#### 1.2.1. apiversion/kind

```
apiVersion: v1
kind: Pod

```

#### 1.2.2. metadata

```
metadata
    name        <string>            # 在一个名称空间内不能重复
    namespace   <string>            # 指定名称空间，默认defalut
    labels      <map[string]string> # 标签
    annotations <map[string]string> # 注释，不能作为被筛选
```

#### 1.2.3. spec

```
spec
    containers  <[]Object> -required-   # 必选参数
        name    <string> -required-     # 指定容器名称，不可更新
        image   <string> -required-     # 指定镜像
        imagePullPolicy <string>        # 指定镜像拉取方式
            # Always: 始终从registory拉取镜像。如果镜像标签为latest，则默认值为Always
            # Never: 仅使用本地镜像
            # IfNotPresent: 本地不存在镜像时才去registory拉取。默认值
        env     <[]Object>              # 指定环境变量，使用 $(var) 引用,参考: configmap中模板
        command <[]string>              # 以数组方式指定容器运行指令，替代docker的ENTRYPOINT指令
        args    <[]string>              # 以数组方式指定容器运行参数，替代docker的CMD指令
        ports   <[]Object>              # 指定容器暴露的端口
            containerPort <integer> -required-  # 容器的监听端口
            name    <string>            # 为端口取名，该名称可以在service种被引用
            protocol  <string>          # 指定协议，默认TCP
            hostIP    <string>          # 绑定到宿主机的某个IP
            hostPort  <integer>         # 绑定到宿主机的端口
        readinessProbe <Object>         # 就绪性探测，确认就绪后提供服务
            initialDelaySeconds <integer>   # 容器启动后到开始就绪性探测中间的等待秒数
            periodSeconds <integer>     # 两次探测的间隔多少秒，默认值为10
            successThreshold <integer>  # 连续多少次检测成功认为容器正常，默认值为1。不支持修改
            failureThreshold <integer>  # 连续多少次检测成功认为容器异常，默认值为3
            timeoutSeconds   <integer>  # 探测请求超时时间
            exec    <Object>            # 通过执行特定命令来探测容器健康状态
                command <[]string>      # 执行命令，返回值为0表示健康，不自持shell模式
            tcpSocket <Object>          # 检测TCP套接字
                host <string>           # 指定检测地址，默认pod的IP
                port <string> -required-# 指定检测端口
            httpGet <Object>            # 以HTTP请求方式检测
                host    <string>        # 指定检测地址，默认pod的IP
                httpHeaders <[]Object>  # 设置请求头
                path    <string>        # 设置请求的location
                port <string> -required-# 指定检测端口
                scheme <string>         # 指定协议，默认HTTP
        livenessProbe   <Object>        # 存活性探测，确认pod是否具备对外服务的能力
            # 该对象中字段和readinessProbe一致
        lifecycle       <Object>        # 生命周期
            postStart   <Object>        # pod启动后钩子，执行指令或者检测失败则退出容器或者重启容器
                exec    <Object>        # 执行指令，参考readinessProbe.exec
                httpGet <Object>        # 执行HTTP，参考readinessProbe.httpGet
                tcpSocket <Object>      # 检测TCP套接字，参考readinessProbe.tcpSocket
            preStop     <Object>        # pod停止前钩子，停止前执行清理工作
                # 该对象中字段和postStart一致
    hostname    <string>                # 指定pod主机名
    nodeName    <string>                # 调度到指定的node节点
    nodeSelector    <map[string]string> # 指定预选的node节点
    hostIPC <boolean>                   # 使用宿主机的IPC名称空间，默认false
    hostNetwork <boolean>               # 使用宿主机的网络名称空间，默认false
    serviceAccountName  <string>        # Pod运行时的服务账号
    imagePullSecrets    <[]Object>      # 当拉取私密仓库镜像时，需要指定的密码密钥信息
        name            <string>        # secrets 对象名
```

#### 1.2.4. k8s和image中的命令

![image.png](https://cdn.nlark.com/yuque/0/2020/png/378176/1579444125794-e27d08bb-7811-44c1-806b-86745f8f9093.png)

#### 1.2.4. 就绪性探测和存活性探测

- 就绪性探测失败不会重启pod，只是让pod不处于ready状态。存活性探测失败会触发pod重启。
- 就绪性探测和存活性探测会持续进行下去，直到pod终止。

### 1.3. 案例

**一般不会单独创建pod，而是通过控制器的方式创建。**

#### 1.3.1. 创建简单pod

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-demo
  namespace: app
  labels:
    app: centos7
    release: stable
    environment: dev
spec:
  containers:
  - name: centos
    image: harbor.od.com/public/centos:7
    command:
    - /bin/bash
    - -c
    - "sleep 3600"
```

```
[root@hdss7-21 ~]# kubectl apply -f http://k8s-yaml.od.com/base_resource/pods/myapp.yaml
[root@hdss7-21 ~]# kubectl get pod -o wide -n app
NAME       READY   STATUS    RESTARTS   AGE   IP           NODE                NOMINATED NODE   READINESS GATES
pod-demo   1/1     Running   0          16s   172.7.22.2   hdss7-22.host.com   <none>           <none>
[root@hdss7-21 ~]# kubectl exec pod-demo -n app -- ps uax
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0   4364   352 ?        Ss   04:41   0:00 sleep 3600
root         11  0.0  0.0  51752  1696 ?        Rs   04:42   0:00 ps uax
```



#### 1.3.2. 带健康检测的pod

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-01
  namespace: app
  labels:
    app: centos7
    release: stable
    version: t1
spec:
  containers:
  - name: centos
    image: harbor.od.com/public/centos:7
    command:
    - /bin/bash
    - -c
    - "echo 'abc' > /tmp/health;sleep 60;rm -f /tmp/health;sleep 600"
    livenessProbe:
      exec:
        command:
        - /bin/bash
        - -c
        - "[ -f /tmp/health ]"
```



## 2. Deployment

### 