# ADSBexchange-pi-image

Raspbian setup for feeding ADSBexchange.com with 1090dump &amp; MLAT

Quick and dirty from base Raspbian image.

## Install 1090 Dump Mutability
## Install MLAT Client
## Install 1090 Dump Exporter
## Install Grafana
## Install Prometheus

## Configure 1090 Dump
```
dpkg-reconfigure 1090dump-mutability
```
## Create adsbexchange-mlat_maint.sh in /home/pi 
```
#!/bin/sh
. /home/pi/config.txt
if [ ${MLAT} ]; then
  while true
   do
    sleep 30
    /usr/bin/mlat-client --input-type dump1090 --input-connect localhost:30005 --lat ${LATITUDE} --lon ${LONGITUDE} --alt ${ALTITUDE} --user ${AD$
  done
else
  exit 0
fi
```
## Create adsbexchange-netcat_maint.sh in /home/pi
```
#!/bin/sh
. /home/pi/config.txt

while true
  do
    sleep 30
    /bin/nc 127.0.0.1 30005 | /bin/nc ${ADSBXCUSTOMURL} ${ADSBXCUSTOMPORT}
  done
```
## Create adsbexchange-dashx.sh in /home/pi
```
#!/bin/sh
. /home/pi/config.txt

while true
  do
    sleep 30
sudo -u pi python3 /home/pi/.local/bin/dump1090exporter --url=http://localhost/dump1090/ --port=9105 --latitude=${LATITUDE} --longitude=${LONGITU$
done
```

## Create adsbexchange-prom.sh in /home/pi
```
#!/bin/sh

/usr/bin/prometheus -storage.local.path "/run/prometheus"
```
## Add to rc.local before exit 0
```
/home/pi/adsbexchange-mlat_maint.sh &
/home/pi/adsbexchange-netcat_maint.sh &
/home/pi/adsbexchange-prom.sh &
/home/pi/adsbexchange-dashx.sh &
```
## Create config.txt in /home/pi/
```
LATITUDE="<insert Lat>"
LONGITUDE="<insert Lon>"
ALTITUDE="<insert Alt>"
ADSBXCUSTOMPORT="<insert port>"
MLATADSBXSERVER="feed.adsbexchange.com:31090"
ADSBXNAME="<give it a name>"
ADSBXCUSTOMURL="feed.adsbexchange.com"
MLAT=1
```



