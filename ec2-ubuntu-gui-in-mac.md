---
title: '[AWS] Mac에서 AWS EC2 Ubuntu GUI 접속'
date: '2023-09-01'
main_category: "TECH"
sub_category: "SQL"
tags: ["AWS", "Mac", "Ubuntu", "GUI"]
---

# 1. EC2 Ubuntu Instance 접속

EC2 Ubuntu Instance에 SSH로 접속한다.

# 2. 필요한 패키지 설치

아래의 명령어를 순서대로 입력하여 필요한 패키지를 설치한다.

```bash
$ sudo apt update
$ sudo apt install ubuntu-desktop
$ sudo apt install tightvncserver
$ sudo apt install gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal
```

# 3. VNC 서버 시작

```bash
$ vncserver
```

이 때, 원격 접속 시 사용할 비밀번호를 설정한다. 이 비밀번호는 잊어버리면 안된다.

# 4. VNC 설정 파일 수정

```bash
$ sudo vi ~/.vnc/xstartup
```

`xstartup` 파일 내용을 아래와 같이 수정한다.

```sh
#!/bin/sh
# Uncomment the following two lines for normal desktop:
# unset SESSION_MANAGER
# exec /etc/X11/xinit/xinitrc
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey vncconfig -iconic &
x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
x-window-manager &
gnome-panel &
gnome-settings-daemon &
metacity &
nautilus &
gnome-terminal &
```

# 5. VNC 서버 재시작

설정을 적용하기 위해 VNC 서버를 재시작한다.

```bash
vncserver -kill :1
vncserver
```

# 6. AWS 보안 그룹 설정 변경

AWS 콘솔에 로그인하고, 해당 EC2 인스턴스의 보안 그룹 설정으로 이동한다. 인바운드 규칙에서 `5901` 포트를 열어준다.

# 7. Mac에서 VNC로 연결

- Mac에서 Finder를 열고, command+k 단축키를 사용하여 VNC 연결 창을 띄운다.
- `vnc://<우분투 instance IP>:5901`로 연결하고, 3단계에서 설정한 비밀번호를 입력하여 로그인한다.

# 만약 1번 포트가 열리지 않은 경우

## 활성 VNC 세션 확인

활성 VNC 세션이 있는지 확인하기 위해 아래의 명령어를 실행한다.

```bash
$ ps aux | grep Xtightvnc
```

만약 프로세스가 존재할 경우 제거한다.

## 잠금 파일 삭제

활성 세션과 연관된 잠금 파일이 없는 경우, 안전하게 잠금 파일들을 제거할 수 있다.

```bash
sudo rm /tmp/.X11-unix/X*
sudo rm /tmp/.X*-lock
```

## 서버 재시작

```bash
$ vncserver
```