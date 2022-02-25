
建議Oracle 12c (12.2.0.1或更高版本)

以下請改好真實Oracle DB訊息

### 1.請先改好目錄 params/ords_params.properties 正確設定  
>  db.sid 通常不帶符號  
>  user.public.password 是安裝時會建立的ORDS_PUBLIC_USER密碼  
>  sys.password sys的密碼  

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
>為了安全請拿掉

    #sys.user=sys
    #sys.password=
    #user.public.password=

### 7.啟動

    docker-compose start


### 8.前往官網學習建置好的使用方式 Getting Started with Oracle REST Data Services  

    https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/21.4/index.html


本範例是啟動9292 port,docker-compose.yml 也是 9292:9292 如果外訪可以只改 <指定port>:9292

### 關於"jdbc.InitialLimit in configuration |apex|pu| is using a value of 3"

jdbc.MaxLimit 設定檔內有效
但
jdbc.InitialLimit 設定檔內無效
暫時解法可以

複製出來

    docker cp  ords_openjdk8:/ords/conf/ords/conf/apex_pu.xml ./

修改追加一行 jdbc.InitialLimit 數值而jdbc.MaxLimit的部分其實設定檔那裏一開始就決定好就好

    vi apex_pu.xml
    <entry key="jdbc.MaxLimit">100</entry>
    <entry key="jdbc.InitialLimit">10</entry>

再蓋回去

    docker cp ./apex_pu.xml  ords_openjdk8:/ords/conf/ords/conf/apex_pu.xml

重新啟動

    docker-compose stop
    docker-compose start

該警告就不會出現了


### 移除  
>初始化的時候，ORDS會連進資料庫安裝環境建立DB USER跟物件，想要移除也是可以的。  
>移除的時候要確認ORDS_PUBLIC_USER已經完全關閉離線。  
>建議先改好目錄 params/ords_params.properties 正確設定，問句預設值會比較不需要設定。  

    docker run  --rm -it  --name ords_inst \
       -v "$(pwd)/params/ords_params.properties:/ords/params/ords_params.properties" \
       dennisxkimo/ords_openjdk8:latest sh uninstall.sh

    Enter number for [1] Basic  [2] TNS  [3] Custom URL [1]:1
    Enter the name of the database server [127.0.0.1]:127.0.0.1
    Enter the database listen port [1521]:1521
    Enter 1 to specify the database service name, or 2 to specify the database SID [1]:2
    Enter the database SID [xe]:
    Requires to login with administrator privileges to verify Oracle REST Data Services schema.

    Enter the administrator username:sys
    Enter the database password for SYS AS SYSDBA:<sys密碼>
    Confirm password:<sys密碼>


應該會刪除兩個DB USER  
ORDS_PUBLIC_USER  
ORDS_METADATA  
