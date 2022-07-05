## 0. Docker
- 애플리케이션(application)을 개발하고 출시(shipping), 작동시키는데 사용되는 오픈 platform이다.
- 도커(Dockdr)를 사용하면 나의 infra를 하나의 관리되는 애플리케이션처럼 다룰 수 있다.
- 코드와 테스트, 배포를 빠르게 하도록 도와주고 코드 작성과 실행 간의 사이클(cycle)을 줄여준다.
- 도커는 커널 컨테이너화 + 애플리케이션을 관리&배포하는 것을 도와주는 도구의 조합!!
- 쿠버네티스(Kubernetes)에서 도커 컨테이너는 를 바로 사용할 수 있다.  (쿠버네티스 엔진에서 동작 가능하도록 되어있기 때문)

1. 도커 이미지를 도커 허브(Hub) 및 Google Container Registry에서 가져오기
2. 도커 이미지를 Google Container Registry로 푸시하기

```
🍆 Google Cloud Shell 🍆
- 명령줄을 통해 GCP(Google Cloud Platform) 리소스에 엑세스할 수 있다.
- `gcloud` 명령줄 도구로 여러 기능을 지원한다.
    - ex) 사용중인 계정 이름 목록 표시하기 - `gcloud auth list`
    - ex) 프로젝트 ID 목록 표시하기 - `gcloud config list project`
``` 
    
## 1. hello-world 컨테이너 실행하기
```shell
docker run hello-world
```
<details>
<summary>Command Output</summary>
<div>

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete
Digest: sha256:13e367d31ae85359f42d637adf6da428f76d75dc9afeb3c21faea0d976f5c651
Status: Downloaded newer image for hello-world:latest
```
</div>
</details>

위 명령어를 실행하면 Docker 데몬이 로컬에서 이미지를 찾고, 없으면 공개(public) 레즈스트리(도커 허브)에서 찾아서 가져와서 **이미지로부터 컨테이너를 생성해서 실행**시킨다.
- 위 명령어를 다시 실행해보면, 도커 데몬이 로컬 레즈스트리에서 이미지를 찾아서 이 이미지에서 컨테이너를 실행한다는 결과가 나온다.<br>
(즉 도커 데몬이 무조건 도커 허브에서 가져올 필요는 없다.) 
<details><summary>Command Output</summary>
<div>

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```
</div></details>
<br>

---

## 2. 도커 허브에서 가져온 컨테이너 이미지 확인하기
```shell
docker images
```
이때 이미지ID는 **SHA256 해시** 형식이다.
해당 필드에서는 프로비저닝된 도커 이미지를 지정하는 것이다.

> 프로비저닝(provisioning)
> 사용자의 요구에 맞게 시스템 자원을 할당, 배치, 배포해 두었다가 필요 시 시스템을 즉시 사용할 수 있는 상태로 **미리 준비**해 두는 것

> 명령어
> - docker ps : 실행 중인 컨테이너 조회
> - docker ps -a : 실행 완료된 컨테이너까지 조회
> <details><summary>Command Output</summary>
> <div>
> 도커가 컨테이너를 식별하기 위한 UUID로 <i>CONTAINER_ID</i>와 관련 메타데이터가 표시된다. 랜덤으로 지정된 <i>NAMES</i>는 특정 이름으로 지정할 수도 있다. (<i>docker run --name [container-name] hello-world</i>)
>
> ```
> CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
> 4c7c08b0c935   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             wizardly_greider
> 52ef439bfda8   hello-world   "/hello"   8 minutes ago   Exited (0) 8 minutes ago             practical_bouman
> ```
> </div></details>
<br>

---

## 3. 빌드
- 간단한 노드 애플리케이션 기반 Docker 이미지 빌드해보자.

1. 'test'라는 폴더 생성 후 해당 폴더로 전환한다.
```
mkdir test && cd test
```
2. DockerFile`이라는 파일명으로 만들어서 아래 내용을 입력한다.
Docker 데몬에 이미지를 빌드하는 방법을 입력한다. (# 은 주석)
- 파일을 만드는 방법은 `cat`을 이용한다.
- 아래와 같이 `EOF`를 붙이면 여러줄에 작성할 수 있다. 마지막에 EOF를 작성하면 끝난다.
```
cat > app.js <<EOF
# 공식 노드 런타임을 상위 이미지로 사용합니다. (노드 버전 6의 공식 Docker 이미지)
FROM node:6
# 컨테이너의 작업 디렉토리를 /app으로 설정합니다.
WORKDIR /app
# 현재 디렉토리 내용을 /app에 있는 컨테이너에 복사합니다.
ADD . /app
# 컨테이너의 포트 80을 외부에 공개하여 공개된 컨테이너 포트에서의 연결을 허용합니다.
EXPOSE 80
# 컨테이너가 시작될 때 노드를 사용하여 app.js를 실행합니다.
CMD ["node", "app.js"]
EOF
```

3. 노드 애플리케이션을 작성한다. 
> app.js : 간단한 HTTP 서버로 포트 80을 수신하고 'Hello World'를 반환하는 애플리케이션
```javascript
const http = require('http');
const hostname = '0.0.0.0';
const port = 80;
const server = http.createServer((req, res) => {
    res.statusCode = 200;
      res.setHeader('Content-Type', 'text/plain');
        res.end('Hello World\n');
});
server.listen(port, hostname, () => {
    console.log('서버 작동 at at http://%s:%s/', hostname, port);
});
process.on('SIGINT', function() {
    console.log('Caught interrupt signal and will exit');
    process.exit();
});
```
<br>

---

## 4. 이미지 빌드
```
docker build -t node-app:0.1 .
```
- `-t` 태그 : {이미지이름}:{태그}  를 이용해 태그를 이용해서 기존 이미지를 지정해야한다. 그렇지 않을 경우 최신 이미지로 설정되서 기존 이미지와 구분이 어려워진다.

- 최신 이미지로 지정된 경우 아래와 같이 나타난다.
```
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   9 months ago   13.3kB
```

<details><summary>Command Output</summary>
<div>
DockerFile에 입력한 명령줄들이 순차적으로 실행되면서 빌드됩니다.
```
Sending build context to Docker daemon  3.072kB
Step 1/5 : FROM node:6
6: Pulling from library/node
c5e155d5a1d1: Pull complete
221d80d00ae9: Pull complete
4250b3117dca: Pull complete
3b7ca19181b2: Pull complete
425d7b2a5bcc: Pull complete
69df12c70287: Pull complete
ea2f5386a42d: Pull complete
d421d2b3c5eb: Pull complete
Digest: sha256:e133e66ec3bfc98da0440e552f452e5cdf6413319d27a2db3b01ac4b319759b3
Status: Downloaded newer image for node:6
 ---> ab290b853066
Step 2/5 : WORKDIR /app
 ---> Running in 8d7e8dd30ab1
Removing intermediate container 8d7e8dd30ab1
 ---> bc72b3829c7c
Step 3/5 : ADD . /app
 ---> 860401485e83
Step 4/5 : EXPOSE 80
 ---> Running in 6e195925e2df
Removing intermediate container 6e195925e2df
 ---> 7982ee132eba
Step 5/5 : CMD ["node", "app.js"]
 ---> Running in e2c2343ec426
Removing intermediate container e2c2343ec426
 ---> bec1e45d49e4
Successfully built bec1e45d49e4
Successfully tagged node-app:0.1
```

</div></details>
<br>

이제 빌드된 이미지를 확인해보자.
```
docker images
```

<details><summary>Command Output</summary>
<div>
- node는 기본 이미지, node-app은 방금 내가 빌드한 이미지이다.
- node-app을 제거해야 node도 제거된다.
- `SIZE`는 VM에 비해 상대적으로 작은데, `node:slim`이나 `node:alpine`과 같은 노드 이미지의 버전을 사용하면 더 작은 이미지를 제공해서 이식성이 높아진다.
```
REPOSITORY    TAG       IMAGE ID       CREATED              SIZE
node-app      0.1       e9fbf922187a   About a minute ago   884MB
hello-world   latest    feb5d9fea6a5   9 months ago         13.3kB
node          6         ab290b853066   3 years ago          884MB
```
</div></details>
<br>

---

## 5. 컨테이너 실행
빌드한 이미지를 기반으로 컨테이너를 실행하자.
- `-p` : 컨테이너의 80번 포트에 호스트의 4000번 포트를 매핑하는 플래그
- `--name` : 컨테이너 이름을 지정하는 플래그
```
docker run -p 4000:80 --name my-first-app node-app:0.1
```

`서버 작동 at http://0.0.0.0:80/` 이라고 뜨네요!

이제 다른 터미널에서 접속해보겠다.
```
curl http://localhost:4000
```
결과는 `Hello World`라고 뜬다.

###  ✋ WAIT! 백그라운드 실행 ✋ 
이제 백그라운드에서 실행시켜보자. 그전에 위에서 실행한 컨테이너를 중단하고 삭제한 후에 실행해보자.
```
docker stop my-first-app && docker rm my-first-app
```

백그라운드 실행!! (`-d` 플래그만 추가하면 된다.)
```
docker run -p 4000:80 --name my-first-app -d node-app:0.1
docker ps   // 컨테이너 실행 중인지 확인!
```

Command output
```
CONTAINER ID   IMAGE          COMMAND         CREATED         STATUS         PORTS                  NAMES
d08796cccb56   node-app:0.1   "node app.js"   5 seconds ago   Up 4 seconds   0.0.0.0:4000->80/tcp   my-first-app
```
<br>
위와 같이 컨테이너 ID가 나타나는데, 해당 컨테이너의 로그를 찍어보자. 
이때 컨테이너 ID를 다 치지 않고 앞 일부(ex- d08)만 적을 수 있다.  

```
docker logs d08
docker logs -f 08 // 실시간으로 로그를 찍어내릴 때 쓰는 명령어
```

결과는 <i>서버 작동 at http://0.0.0.0:80/</i>이라고 뜬다.

<br>

###  ✋ WAIT! 다른 버전 빌드 ✋
- app.js에서 서버 작동 시 출력되는 메시지를 조금 수정하고 0.2 버전으로 다시 빌드해보자.
- 본인은 vim을 이용해서 `Welcome to Google Cloud. by moonz.`로 바꿨다.
- ⚠️ 주의 : 문자열 수정할 때 `'`를 넣으려면 `\'`로 넣어야 한다.

<br>
빌드!

```
docker build -t node-app:0.2 .
```

### 🌟NEW KNOWLEDGE🌟 
- 이때 출력되는 결과를 보면, 2번째 단계에서 cache를 사용한다고 뜨는데, 기존 캐시 레이어를 사용하는 것이다.
- 그리고 `app.js`를 수정했기 때문에 3번째 단계에서부터는 레이어가 수정된다.
```
Sending build context to Docker daemon  3.072kB
Step 1/5 : FROM node:6
 ---> ab290b853066
Step 2/5 : WORKDIR /app
 ---> Using cache
 ---> 55db9c90673c
Step 3/5 : ADD . /app
 ---> 7f31ed0e1ebb
Step 4/5 : EXPOSE 80
 ---> Running in 41384b847cc4
Removing intermediate container 41384b847cc4
 ---> e8fade25974f
Step 5/5 : CMD ["node", "app.js"]
 ---> Running in 7fd04f745545
Removing intermediate container 7fd04f745545
 ---> 729a1a435889
Successfully built 729a1a435889
Successfully tagged node-app:0.2
```
<br>

위 과정과 마찬가지로 run한 후 `docker ps`로 실행 중인 컨테이너를 확인하면 다음과 같이 두개가 보인다. 
다른 터미널에서 실행해보자.
```
CONTAINER ID   IMAGE          COMMAND         CREATED          STATUS          PORTS                  NAMES
e19535e7abaf   node-app:0.3   "node app.js"   8 seconds ago    Up 7 seconds    0.0.0.0:8080->80/tcp   my-first-app-ver3
d08796cccb56   node-app:0.1   "node app.js"   19 minutes ago   Up 19 minutes   0.0.0.0:4000->80/tcp   my-first-app
```

다른 터미널에서 접속해보면 다음과 같이 다르게 출력된다.
![image](https://user-images.githubusercontent.com/66311276/177313346-7d8cd7d9-1e4d-4e02-9357-3ad8f6a043af.png)


## 6. 디버깅
1. 현재 동작(running) 중인 컨테이너 내에서 대화식 Bash session을 시작할 수 있다.
```
docker exec -it [container_id] bash
```

이때 Dockerfile`에 명시된 `/app` 디렉토리 `WORKDIR`에서 bash가 시작된다.<br>
이제 debug할 컨테이너 내부에서 대화식 shell session을 할 수 있다.

> <i>exit</i>을 이용해 빠져나온다.

<br>

2. 특정 컨테이너의 <b>메타데이터</b>를 검토할 수 있다.
- `--format` 플래그로 아래 json 중 특정 필드를 검사할 수 있다.
```
docker inspect [container_id]
docker inspect --format='{{필드명.필드명}}' [container_id]

// --foramt 플래그 예시1
docker inspect --format='{{.State.Status}}' d0

// command output
running

// --format 플래그 예시2
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container_id]

```

<details>
<summary>Command output</summary>
<div>

```
[
    {
        "Id": "d08796cccb562b6302bdf97cda3a895732d8bcbf74c9bfd591805e71a3ef9cd2",
        "Created": "2022-07-05T10:09:21.485131759Z",
        "Path": "node",
        "Args": [
            "app.js"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 1305,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-07-05T10:09:21.863416896Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:e9fbf922187a8feafd2e26a5c0e692ff554ed2407ea62feb9a323b57ad8cd942",
        "ResolvConfPath": "/var/lib/docker/containers/d08796cccb562b6302bdf97cda3a895732d8bcbf74c9bfd591805e71a3ef9cd2/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/d08796cccb562b6302bdf97cda3a895732d8bcbf74c9bfd591805e71a3ef9cd2/hostname",
        "HostsPath": "/var/lib/docker/containers/d08796cccb562b6302bdf97cda3a895732d8bcbf74c9bfd591805e71a3ef9cd2/hosts",
        "LogPath": "/var/lib/docker/containers/d08796cccb562b6302bdf97cda3a895732d8bcbf74c9bfd591805e71a3ef9cd2/d08796cccb562b6302bdf97cda3a895732d8bcbf74c9bfd591805e71a3ef9cd2-json.log",
        "Name": "/my-first-app",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {
                "80/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "4000"
                    }
                ]
            },
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/7c0be2a763edba955f256af87c9a72c0a1a4de14108979209f7d4875aba59843-init/diff:/var/lib/docker/overlay2/3579c8062856488e663274a9e7784b082ee0c7fef72af34791a4d2dc949548eb/diff:/var/lib/docker/overlay2/363d5312da0b3ac8fc9dd81832e721953ebe17b587b60c4a740f7a4b0e3a5b40/diff:/var/lib/docker/overlay2/87323a2cad2ef318dba60bb437c68a6530ac29f60958f2e663bef0a0b85dd029/diff:/var/lib/docker/overlay2/e891c23a911cfaac2cebfe1570395662f55dcc7200d83a2b35df43b6d7b71066/diff:/var/lib/docker/overlay2/bcd6649de2cfcd8f208165656dd23d9b5b773923592b149d86f8bf73551fe1cd/diff:/var/lib/docker/overlay2/3d182d0c24db7e62e096a34cc5c2b3e97d84926b14f9c77cf186bda3e7940332/diff:/var/lib/docker/overlay2/8f05a02fd418e6fe227d32c20c0d5577a74be40c43d49085875aee1e12c60600/diff:/var/lib/docker/overlay2/c872d1f03abf433d145075036332b43769c21d5fedacee49400b6da4eb1c69b5/diff:/var/lib/docker/overlay2/22c80aa9d7a0d40464360c88048c75121463e89244f59e555090483d173fe472/diff:/var/lib/docker/overlay2/e94d882e3ddc3d5405ce2c3d8b187ea3a8a2b07fba7c368108f248653d0c1cf4/diff",
                "MergedDir": "/var/lib/docker/overlay2/7c0be2a763edba955f256af87c9a72c0a1a4de14108979209f7d4875aba59843/merged",
                "UpperDir": "/var/lib/docker/overlay2/7c0be2a763edba955f256af87c9a72c0a1a4de14108979209f7d4875aba59843/diff",
                "WorkDir": "/var/lib/docker/overlay2/7c0be2a763edba955f256af87c9a72c0a1a4de14108979209f7d4875aba59843/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "d08796cccb56",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NODE_VERSION=6.17.1",
                "YARN_VERSION=1.15.2"
            ],
            "Cmd": [
                "node",
                "app.js"
            ],
            "Image": "node-app:0.1",
            "Volumes": null,
            "WorkingDir": "/app",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "25594fef219c2018036b67c90a9e358fc97ae240085612b1ed4c105c038e4a12",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "80/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "4000"
                    }
                ]
            },
            "SandboxKey": "/var/run/docker/netns/25594fef219c",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "0ca7f9041fedde2a4e7eedc18c7ee2eaf1ea9e350ad91c19f0b3c9ce8c19bf92",
            "Gateway": "172.18.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.18.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:12:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "54621e227be971f110aab37b04cb82cd9ca2eb5b8a0093040a7ce37f0163dfec",
                    "EndpointID": "0ca7f9041fedde2a4e7eedc18c7ee2eaf1ea9e350ad91c19f0b3c9ce8c19bf92",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```
<div></details>

<br>

---

## 7. 게시



### Q&A
1. `Dockerfile`이라는 파일명을 이용해서 빌드를 해야하는 거같은데 도커를 빌드하는데 사용되는 파일명으로 지정된 건가?

> Dockerfile : Docker 데몬에 이미지를 빌드하는 방법을 지시하는 파일
