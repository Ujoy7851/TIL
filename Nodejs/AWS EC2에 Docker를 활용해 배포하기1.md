Node.js로 개발한 서버를 배포하기 위해 AWS EC2 인스턴스를 생성하고, Jupyter Notebook을 이용해서 인스턴스에 접속하고 관리하는 방법에 대해 알아보자.

## EC2 인스턴스 생성

AWS 웹사이트에 접속해 EC2 콘솔로 이동한다. `인스턴스 시작` 버튼을 클릭해 새로운 인스턴스를 생성을 시작한다. Ubuntu 18.04 버전을 선택하고, AWS에서 무료로 제공하는 t2.micro 옵션의 기본 구성으로 인스턴스를 생성한다.

마지막으로 SSH를 통해 안전하게 인스턴스에 접속하기 위해 필요한 키페어도 생성해주어야 한다. 원하는 이름을 설정해주고 나면 .pem 파일이 다운로드되는데 이 파일을 통해 인스턴스에 마음대로 접근할 수 있기 때문에 유출되지 않도록 안전한 위치에 저장해준다.

window에서 인스턴스에 연결할 때는 SSH 클라이언트를 사용한다. 콘솔에서 `ssh` 명령어를 입력해 로컬 컴퓨터에 SSH가 설치된지 확인한다. 설치되지 않은 경우 OpenSSH나 PuTTY를 설치해 연결할 수 있다.

SSH 클라이언트를 사용해 인스턴스에 연결하는 경우 개인 키 파일의 보안 권한을 설정해서 자신만(관리자) 읽을 수 있도록 해야한다.

- window의 경우, 파일을 우클릭해서 속성-보안 탭-고급을 선택하고, 상속 사용 안함으로 설정한 뒤 관리자(Administrators, SYSTEM)만 남기고 제거해준다.
- mac 또는 Linux의 경우, `chmod 0400 .ssh/my_private_key.pem` 명령을 실행해준다.

보안 권한이 설정되었으면, 명령프롬프트 관리자 권한으로 실행한 뒤, 개인키 파일이 있는 폴더로 이동한다. 마지막으로 EC2 인스턴스 콘솔에서 `연결` 버튼을 클릭하면 나오는 명령어를 입력하면 인스턴스에 연결된다.

```bash
ssh -i /path/my-key-pair.pem my-instance-user-name@my-instance-public-dns-name
```

---

## Jupyter Notebook 설치

### Jupyter Notebook 설치

매번 위 방식처럼 콘솔에서 SSH를 이용해 인스턴스에 접속하기 불편하기 때문에 브라우저를 이용해 쉽게 서버에 접근하고 관리할 수 있도록 Jupyter Notebook을 설치한다.

```bash
sudo apt-get update
sudo apt-get install python3-pip
sudo pip3 install notebook
```

### 비밀번호 설정

Jupyter Notebook이 설치되면 사용자 지정 비밀번호와 SSL/HTTPS를 적용해 서버의 보안을 강화해야 한다. 우선 다음 명령어로 Jupyter notebook 구성 파일을 만든다.

```bash
jupyter notebook --generate-config
sudo vi 환경설정파일경로
```

다음으로 passwd() 함수를 이용해 비밀번호를 설정하면 해쉬값을 반환한다. 구성 파일에 방금 전 반환된 비밀번호 해쉬값 등을 작성한다.

```bash
python3
>>> from notebook.auth import passwd
>>> passwd()
```

```bash
환경설정파일 아래쪽에 작성

c = get_config()
c.NotebookApp.password = 해쉬값
c.NotebookApp.ip = '서버 내부 ip'
c.NotebookApp.notebook_dir = '/'
```

### HTTPS 적용

해시 된 암호가 브라우저에서 암호화되지 않은 상태로 전송되지 않도록 웹 인증서와 SSL을 사용하는 것이 좋다. 자체 서명 된 인증서를 사용할 수도 있으나 Let's Encrypt를 통해 무료 SSL 인증서를 받아 서버를 설정하도록 해보겠다. 인증서 발급 방법은 [이 포스트](https://github.com/Ujoy7851/TIL/blob/master/Nodejs/nginx%EC%97%90%EC%84%9C%20Let's%20Encrypt%EB%A1%9C%20SSL%20%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0.md)에 설명해두었다. (https 적용을 위해 인증서를 발급받기 전 도메인이 등록되어 있어야 한다.)

설정이 완료된 후 root 권한으로 Jupyter notebook을 실행해보면 8888 포트로 Jupyter 서버가 열린 것을 확인할 수 있다. EC2 콘솔로 이동해 보안 그룹에서 인바운드 규칙을 편집해 8888 포트를 열어주면 브라우저를 통해 접속할 수 있다.

```bash
sudo jupyter-notebook --allow-root
```

Jupyter notebook을 백그라운드에서 항상 실행 중인 상태로 만들고 싶다면 `ctrl + z`로 잠시 프로세스를 중단하고 다음 명령어를 입력한다.

```bash
bg
disown -h

실행 종료를 위해 pid를 알아내 종료시킨다.
sudo netstat -nap | grep 8888
sudo kill -9 pid
```

### 시스템 서비스 설정

서버가 재부팅되어도 자동으로 Jupyter notebook이 실행될 수 있도록 시스템 서비스로 등록해준다. 우선, jupyter notebook의 설치 경로를 확인한 뒤 서비스 파일을 생성한다.

```bash
which jupyter-notebook
sudo vi /ect/systemd/system/jupyter.service
```

파일에 아래와 같이 작성한다.

```bash
[Unit]
Description=Jupyter Notebook Server

[Service]
Type=simple
User=ubuntu
ExecStart=/usr/bin/sudo 주피터설치경로 --allow-root --config=/home/ubuntu/.jupyter/jupyter_notebook_config.py

[Install]
WantedBy=multi-user.target
```

위에서 만든 시스템 서비스를 구동시켜준다.

```bash
sudo systemctl daemon-reload
sudo systemctl enable jupyter
sudo systemctl start jupyter

구동확인
sudo systemctl status jupyter

재시작
sudo systemctl restart jupyter
```