## Let's Encrypt 인증서 설치

Let's Encrypt를 이용해 무료 SSL 인증서를 설치한다. 우선 Let's Encrypt에서 인증서를 받기 위해 Certbot을 설치해준다.

```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt update
sudo apt install certbot python-certbot-apache
```

위 방식처럼 패키지 매니저를 통한 설치가 실패한 경우 certbot-auto를 다운로드하여 사용한다.

```bash
wget https://dl.eff.org/certbot-auto
chmod a+x ./certbot-auto
```

certbot 설치가 완료되면 다음 명령어를 사용해 인증서를 발급받는다. 이때 80번, 443번 포트에 실행중인 프로세스가 있다면 먼저 종료시켜줘야 한다.

```bash
sudo certbot --nginx certonly -d [도메인네임1], [도메인네임2]
```

`certonly`옵션은 인증서 발급만하고 서버의 설정 파일에 인증서와 관련된 내용을 임으로 수정하지 않도록 해준다.

또 다른 인증서 발급 옵션으로 `--webroot`, `--Standalone`, `--preferred-challenges dns` 중 하나를 선택해 인증서 방식을 지정할 수도 있다.

위 명령어를 입력하고 나서 관리자 이메일을 입력하고 이용약관 및 뉴스레터 수신 여부에 대한 질문에 답하면 발급이 완료된다. 인증서가 생성되면 `/etc/letsencrypt/live/[도메인명].com` 경로아래 인증서 파일 4개가 생성된다.

---

## Nginx 설치 및 환경설정

Ubuntu에 nginx를 설치한다.

```bash
sudo apt-get install nginx
```

nginx가 설치되면 /etc/nginx/conf.d 폴더 안에 .conf 확장자를 가진 환경 설정 파일을 만들어 nginx 웹 서버에 대한 설정을 할 수 있다.

/etc/nginx/conf.d 폴더 아래 `server.conf` 파일을 생성해서 위에서 발급한 SSL 인증서의 키 경로를 설정하고 http로 접속한 경우 https로 리다이렉트할 수 있도록 아래와 같이 작성한다.

```bash
server {
				listen 80;
        listen [::]:80;
        server_name [도메인주소1] [도메인주소2];
        return 301 [도메인주소];
}

server {
        listen 443 ssl http2 default_server;
        listen [::]:443 ssl http2 default_server;
        server_name [도메인주소1] [도메인주소2];

        location / {
                proxy_pass http://localhost:3000;
        }
		    ssl_certificate /etc/letsencrypt/live/[도메인명]/fullchain.pem; # managed by Certbot
		    ssl_certificate_key /etc/letsencrypt/live/[도메인명]/privkey.pem; # managed by Certbot
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;

        ssl_session_cache shared:SSL:5m;
        ssl_session_timeout 1h;
        add_header Strict-Transport-Security max-age=15768000 always;
}
```

모든 설정이 끝났으면 nginx를 시작한다.

```bash
sudo service nginx start
```

설정 파일을 수정한 경우에는 재시작이 필요하다. 다음은 nginx 관련 명령어다.

```bash
// 시작
$ sudo service nginx start
$ sudo systemctl start nginx
$ sudo /etc/init.d/nginx start

// 재시작
$ sudo service nginx restart
$ sudo systemctl restart nginx
$ sudo /etc/init.d/nginx restart

// 중지
$ sudo service nginx stop
$ sudo systemctl stop nginx
$ sudo /etc/init.d/nginx stop

// 상태
$ sudo service nginx status
$ sudo systemctl status nginx

// 설정 reload
$ sudo service nginx reload
$ sudo systemctl reload nginx
$ sudo nginx -s reload

// 설정파일 문법 체크
$ sudo nginx -t
```