logArchive.sh

Система архивирование логов сетевых устройств.

Логи сохраняются посредством программы rsyslog в соотвтетсвующие папки разбитые по годам, месяцам и дням.
Для етого соответствующим образом настраивается rsyslog

1. Включаем прием логов с сетевых устройств
   /etc/sysconfig/rsyslog
     SYSLOGD_OPTIONS="-m 0 -r514 -x"
2. Настраивем rsyslog чтоб он сохранял логи в соответсвующем формате
   /etc/rsyslog.conf
     :programname, isequal, "dhcpd"  /var/log/dhcpd.log

     $template LogFormat,"%TIMESTAMP:::date-rfc3339% %fromhost-ip% %HOSTNAME% %syslogtag%%msg:::sp-if-no-1st-sp%%msg:::drop-last-lf%\n"
     $template DailyPerHostLogs,"/var/log/devices/%$YEAR%/%$YEAR%-%$MONTH%/%$YEAR%-%$MONTH%-%$DAY%/%fromhost-ip%_%$YEAR%%$MONTH%%$DAY%_%HOSTNAME%.log"

     *.* -?DailyPerHostLogs;LogFormat
  Теперь rsyslog будет сохранять логи в папках /var/log/devices/<год>/<год>-<месяц>/<год>-<месяц>-<день>/<ip>....log
3. Добавляем ссылку в крон
  ln -s logArchive.sh /etc/cron.monthly/logArchive.sh
