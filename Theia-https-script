#!/bin/bash

set -e
echo "============"
echo "準備開始用 Docker 執行 Theia"
echo "------------"
echo "確認 Theia 容器是否存在"
echo "------------"

if [ ! "$(docker ps -q -f name=theia-https)" ]; then
    if [ "$(docker ps -aq -f status=exited -f name=theia-https)" ]; then
	    read -p "找到存在的容器是否執行？(Y/n)" -n 1 -r
        echo
	    if [[ $REPLY =~ ^[Yy]$ ]]
	    then
            echo "啟動中"
	        docker start theia-https            
            exit 1
        else
            read -p "是否刪除已存在的容器，重新建立?(Y/n)"
            echo
            if [[ $REPLY =~ ^[Yy]$ ]]
            then
                docker rm theia-https
            else
                echo "什麼都不做的結束了..."
                exit 1
            fi    
	    fi
    fi
else
    echo "Theia 容器執行中"
    read -p "是否停止執行中的 Theia 容器？(Y/n)" -n 1 -r
    echo
    if [[ $REPLY =~ ^[Yy]$ ]]
    then
        echo "開始停止"
        docker stop theia-https
        exit 1
    fi
    exit 1
fi

echo "容器不存在，以執行緒的方式啟動"
echo "請回答一些問題，以確認如何建立 Docker 容器"
echo "------------"
echo "請確認是否在終止的時候刪除容器，所有對容器的變更都不會被保留(例如插件)"
read -p "是否在終止的時候，同時刪除容器？(Y/n)" -n 1 -r
if [[ $REPLY =~ ^[Yy]$ ]]
then
    echo
    echo "-------------"
    echo "停止 Theia 時，刪除所有資料"
    echo "-------------"
    m_NeedRmCocntainer=true
else
    echo
    m_NeedRmCocntainer=false
fi

read -p "請輸入登入用的密碼(12345):" -s m_Password
echo
read -p "請再次輸入:" -s
echo
echo "-------------"
if [ $m_Password == $REPLY ]
then
    echo "密碼確認成功"
    if [ -z "$m_Password" ]
    then
        m_Password=12345
    fi
else
    echo "密碼錯誤，結束程式"
    exit 1
fi
echo "-------------"

read -p "請選擇要監聽的 Port (10080):" m_Port
if [ -z "$m_Port" ]
then
    m_Port=10080
fi
echo "-------------"

read -p "請選擇要指向的實體資料夾路徑(/home/project):" m_Path
if [ -z "$m_Path" ]
then
    m_Path="/home/project"
fi
echo "-------------"

echo "請確認以下資訊"
echo "是否在結束後刪除容器 -> ${m_NeedRmCocntainer}"
echo "登入密碼 -> ${m_Password}"
echo "監聽的 Prot -> ${m_Port}"
echo "指向的實體資料夾 -> ${m_Path}"
echo "-------------"
read -p "資訊是否正確?(Y/n)" -n 1 -r 
echo
if [[ $REPLY =~ ^[Yy]$ ]]
then
    echo "確認正確，開始啟動程式，並輸出 Log"
    echo "如果終止，再次執行本 Script 即可背景啟動程式，執行 Log 請於 Docker log 中尋找"
    echo "=============="
    if $m_NeedRmCocntainer
    then
        docker run --rm -it -p ${m_Port}:10080 --name theia-https -e server=:10080 -e secure=0 -e token=${m_Password} -v ${m_Path}:/home/project theiaide/theia-https
    else
	docker run -it -p ${m_Port}:10080 --name theia-https -e server=:10080 -e secure=0 -e token=${m_Password} -v ${m_Path}:/home/project theiaide/theia-https
    fi
 else
    echo "取消執行"
    exit 1
fi
