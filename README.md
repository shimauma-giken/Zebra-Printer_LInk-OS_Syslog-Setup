## ■ 1. Syslog

syslogとは「System Logging Protocol」を略した言葉で、システムログなどをsyslogサーバーと呼ばれる特定のサーバーに送信するために使用されるプロトコルのこと。機器の集中管理や遠隔監視で利用されることが多い。

Link-OSプリンタでもシステム・イベントログをプリンタ内に保存、もしくは、Syslogサーバに通知可能。多拠点・多数のネットワークプリンタの監視・問題発生時の早期解決に役立つ。

耐障害性が向上するため、ゼブラ・テクノロジーズは設定を推奨している。

[参考：SANKO IB/無線LANの運用管理が楽になる？縁の下の力持ち「Syslog サーバ」の導入効果とは？ ](https://sanko-ib.co.jp/mail/blog-wifi_05-syslog/)

[参考：AppNote Syslog ](./AppNote-SYSLOG-v4.pdf)


<img width="500" src="https://sanko-ib.co.jp/mail/blog-wifi_05-syslog/img/syslog-msg.jpg">

</br>

### 設定

```
! U1 setvar "device.syslog.enable" "on"
! U1 setvar "device.syslog.configuration" "DEBUG,LOCAL;DEBUG,192.168.4.60;INFO,192.168.4.133"
! U1 setvar "device.syslog.save_local_file" "yes"
! U1 setvar "device.syslog.log_max_file_size" "400000"


! U1 getvar "device.syslog.enable"
! U1 getvar "device.syslog.configuration"
! U1 getvar "device.syslog.save_local_file"
! U1 getvar "device.syslog.log_max_file_size"

"on""DEBUG,LOCAL;DEBUG,192.168.4.60;DEBUG,192.168.4.100""yes""400000"
```

</br>

### プリンタ内のログ確認方法

1. FTPクライアントから接続し、Syslog ログを取得。
2. USBメモリにコピーする。（USB Hostポート付きのプリンタのみ）
3. コマンドにてログ内容を取得。

```
! U1 getvar "device.syslog.entries"
```

[Support Community: Capturing Syslog for a Link-OS Compatible Printers](https://supportcommunity.zebra.com/s/article/000026448?language=en_US)



</br>

### 例、 Syslog サーバが受信したログ

```
zebra@raspberry:/var/log $ tail -f messages
Dec 30 20:33:31 192.168.4.149 : [conn99.1][Informational][0X1001E] Server certificate expire date: Jun 12 21:54:35 2025 GMT
Dec 30 20:33:31 192.168.4.149 : [conn99.1][Informational][0X10013] Server certificate common name: avs.zpc.zebra.com (matched)
Dec 30 20:33:31 192.168.4.149 : [conn99.1][Informational][0X1002E] Server certificate serial number: CC:AB:A0:F0:BA:96:5F:C0
Dec 30 20:33:31 192.168.4.149 : [conn99.1][Informational][0X1002F] Server certificate fingerprint: AF:73:F3:A9:B5:DB:63:BA:C5:35:FB:35:C5:56:10:1C:3D:1D:47:2D
Dec 30 20:33:31 192.168.4.149 : [conn99.1][Informational][0X10030] Server certificate issuer: C=US; ST=Illinois; O=Zebra Technologies; CN=ZPC
Dec 30 20:33:31 192.168.4.149 : [conn99.1][Informational][0X10038] Server certificate verify ok
Dec 30 20:33:32 192.168.4.149 : [conn99.1][Informational][0X1012] HTTP/1.1 101 Switching Protocols
Dec 30 20:33:32 192.168.4.149 : [conn99.1][Informational][0X10007] Content Length is 0
Dec 30 20:33:32 192.168.4.149 : [conn99.1][Informational][0X101B] Successfully connected
Dec 30 20:33:55 192.168.4.149 : [SGD][Informational][0X1D] SGD Set media.speed -> 5.0
```


[参考：Configuring a Centralized Syslog Server with Syslog-ng on Ubuntu 20.04](https://reintech.io/blog/centralized-syslog-server-configuration-syslog-ng-ubuntu)


Eg. Sample Settings (/etc/syslog-ng/syslog-ng.conf)
```
source s_network {
    syslog(ip(0.0.0.0) port(514) transport("udp"));
    syslog(ip(0.0.0.0) port(514) transport("tcp"));
};

destination d_logs {
    file("/var/log/zebra_$YEAR-$MONTH-$DAY.log" create_dirs(yes));
};

log { source(s_network); destination(d_logs); };
```

</br>