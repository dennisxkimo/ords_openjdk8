### 1.請先改好目錄 params/ords_params.properties 正確設定  
>(Note: db.sid 通常不帶符號)  

    db.hostname=127.0.0.1
    db.port=1521
    db.sid=xe
    db.servicename=xe
    sys.user=sys
    sys.password=
    user.public.password=

### 2.修改正確的docker-compose.yml

    ORACLE_DB_SID: xe
    ORACLE_DB_IP: 127.0.0.1

### 3.啟動

    docker-compose up -d

### 4.查看確認正常

    docker logs  ords_openjdk8  -f

    # 看到這個就可以CTRL+C 離開
    Oracle REST Data Services version : 21.4.1.r0250904
    Oracle REST Data Services server info: jetty/9.4.44.v20210927

### 5.初始化資料完畢，停止  

    docker-compose stop

### 6.修改params/ords_params.properties  
>數值拿掉並註解(初始化已經寫入conf所以不需要這設定了)

    #sys.user=sys
    #sys.password=
    #user.public.password=

### 7.啟動

    docker-compose start


### 8.前往官網學習建置好的使用方式

    https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/21.4/index.html


本範例是啟動9292,docker-compose.yml 也是 9292:9292 如果外訪可以只改 <指定port>:9292
