Настройка системы системы видеонаблюдения на базе FFmpeg, Linux и ZoneMinder

Выбор ПО ZoneMinder обусловлен рядом преимуществ:
- бесплатность
- неограниченное количество каналов
- поддержка огромного парка оборудования
- запись архива в виде отдельных .jpeg картинок

При организации системы видеонаблюдения на базе аналоговых плат видеозахвата не всегда удаётся подружить платы с ZoneMinderom - многие модели он не видит,
но зато он хорошо дружит с ip - камерами, поэтому необходимо порты платы видеозахвата превратить в ip-камеры.
Для этого понадобиться библиотека ffmpeg и её компонент ffserver.

Для начала ставим ffmpeg:
$ sudo apt update 
$ sudo apt install ffmpeg

Идём в /etc/ffserver.conf и приводим к такому виду:

HTTPPort 8090
HTTPBindAddress 0.0.0.0
MaxHTTPConnections 2000
MaxClients 1000
MaxBandwidth 2048
CustomLog -

<feed cam0.ffm>
File /tmp/cam0.ffm
FileMaxSize 8M
#ACL allow 127.0.0.1
</feed>

<feed cam1.ffm>
File /tmp/cam1.ffm
FileMaxSize 8M
#ACL allow 127.0.0.1
</feed>

Stream cam0.mjpg>
Feed cam0.ffm
Format mpjpeg
VideoFrameRate 4
VideoIntraOnly
VideoSize 640x480
NoAudio
Strict -1
</Stream>

Stream cam1.mjpg>
Feed cam1.ffm
Format mpjpeg
VideoFrameRate 4
VideoIntraOnly
VideoSize 640x480
NoAudio
Strict -1
</Stream>

Параметры VideoFrameRate и VideoSize в зависимости от вашего оборудования.

Далее нам нужно выполнить последовательность команд:

ffserver -f /etc/ffserver.conf &                                                        //создаёт потоки для трансляции согласно конфига
ffmpeg -standard pal-DK -s 640x480 -i /dev/video0 http://localhost:8090/cam0.ffm &      //транслирует видео в заданных параметрах в заданный поток с заданного источника
ffmpeg -standard pal-DK -s 640x480 -i /dev/video1 http://localhost:8090/cam1.ffm &

Так-же все вышеперечисленные команды можно ислпользовать в скриптах и добавить в автозагрузку, и вообще линукс почти не ограничивает Вашу фантазию))

Добавление камер в ZoneMinder происходит по следующему шаблону:
![](https://github.com/25RUS/instructions/blob/master/images/video_surveillance/123.png)
![](https://github.com/25RUS/instructions/blob/master/images/video_surveillance/1234.png)
 



