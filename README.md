## Пользователи и права доступа
1) Создать директорию `/var/www/server-app`; в ней файлы `readme.md` и `app.log`; директорию `uploads`
<img width="496" height="122" alt="изображение" src="https://github.com/user-attachments/assets/790afd9c-1908-472a-b824-4ba694592106" />


2) Создать:
- группу `ftpusers`
- группу `admins`
- группу `auditors`
- пользователя `logger` с домашней директорией `/opt/logger` и без возможности входа в систему
<img width="505" height="121" alt="изображение" src="https://github.com/user-attachments/assets/ea7c901b-db08-41ce-9d8b-87c43dbf83a7" />


3) Разрешить с помощью ACL:
- пользователю `www-data` чтение всех файлов
- группе `ftpusers` запись в `uploads`
- группе `admins` полный доступ 
- пользователю `logger` полный доступ только к файлу `app.log`
- группе `auditors` чтение файла `app.log`
4) Настроить систему так, чтобы новые файлы создавались с правами `rw-r-----`, а директории - `rwxr-x---`

<img width="516" height="626" alt="изображение" src="https://github.com/user-attachments/assets/800a0f61-282e-4b0b-970e-c206bed1f5fa" />


5) Разрешить пользователю `logger` вход в систему; переключиться на него с помощью `su`, сохранив текущее окружение и создать файл `/tmp/logger-envs` и вывести в него переменные окружения. Выйти и переключиться еще раз с `-`; сравнить вывод `env` и содержимое файла `/tmp/logger-envs`

<img width="1107" height="603" alt="изображение" src="https://github.com/user-attachments/assets/61499526-f850-42b3-866d-79f8f79436f3" />


6) Без переключения пользователя создать от имени `logger` директорию `/tmp/logger-dir`

<img width="725" height="65" alt="изображение" src="https://github.com/user-attachments/assets/0aab6350-210c-4e49-a93a-dc467d861d17" />

## Анализ производительности

1) Определите текущую load average; установить пакет `sysstat`

<img width="1042" height="96" alt="изображение" src="https://github.com/user-attachments/assets/60c8c7fc-089b-45ab-9157-86f37084d6fa" />


2) Запустить стресс-тест с помощью утилиты `stress-ng`:
- `stress-ng --cpu 4 --timeout 600`
- `stress-ng --vm 2 --vm-bytes 2G --timeout 300 &`
- `stress-ng --vm 1 --vm-bytes 1G --timeout 300 -s 1`

<img width="1860" height="913" alt="изображение" src="https://github.com/user-attachments/assets/b5e56c52-fe2f-4a6b-8e86-30b6962c86a1" />
<img width="1860" height="913" alt="изображение" src="https://github.com/user-attachments/assets/4367da46-e6ef-4a42-919b-33cda40e0955" />
<img width="1860" height="913" alt="изображение" src="https://github.com/user-attachments/assets/3334d4cb-d4b0-4428-b9d2-6d93633e95f5" />

3) Понаблюдать за тем что показывают утилиты
- `dstat -cglmns --top-cpu --top-mem --top-io`
- `mpstat -P ALL`

<img width="1286" height="750" alt="изображение" src="https://github.com/user-attachments/assets/93f4ed82-37c0-4602-90ca-2010b42f36af" />
<img width="1284" height="267" alt="изображение" src="https://github.com/user-attachments/assets/f4698ee2-ed0d-4d16-8b8c-bc76742560ef" />

4) Запустить `dd if=/dev/zero of=/tmp/testfile bs=1M count=500` и параллельно с помощью iostat найти
- устройство с максимальной нагрузкой
- %util диска
- await (среднее время ожидания)

<img width="630" height="415" alt="изображение" src="https://github.com/user-attachments/assets/922d97d9-8040-4738-ae66-a2a8ded52ccb" />

5) Самостоятельно изучить такие утилиты как `atop` и `sar`; сгенерировать нагрузку с помощью `stress-ng` и `dd` и проанализировать _постфактум_ с помощью `atop` и `sar` (то есть условно 5 минут понагружали, сняли нагрузку и анализируем что там было)

<img width="1275" height="689" alt="изображение" src="https://github.com/user-attachments/assets/b9aea130-87a6-4ca5-a802-00043857a17b" />
<img width="1275" height="689" alt="изображение" src="https://github.com/user-attachments/assets/6fa085c9-d441-4b53-8857-e44734a91d03" />
<img width="775" height="791" alt="изображение" src="https://github.com/user-attachments/assets/601dbc43-97e2-406c-abb0-672455324f28" />
<img width="942" height="796" alt="изображение" src="https://github.com/user-attachments/assets/c1e208f8-eb74-4728-bafe-2701f3055e06" />

6) Запустить `openssl speed -seconds 30 2>& 1 > /dev/null &` и с помощью `pidstat` найти
- PID процесса openssl
- % пользовательского (usr) и системного (sys) CPU time

<img width="901" height="768" alt="изображение" src="https://github.com/user-attachments/assets/1a3c82ce-51d4-41bb-985d-815a656dd658" />

7) Запустить `sleep 5; ls -R /usr` и после ее завершения найти
- время выполнения (real, user, sys)
- максимальное потребление памяти

<img width="903" height="218" alt="изображение" src="https://github.com/user-attachments/assets/4e5e3be1-2d72-4314-aa1d-da213ebf4082" />

8) Запустить стресс-тест с помощью утилиты `stress-ng` (параметры подобрать самостоятельно) и вывести с помощью `ps` топ-5 процессов (по очереди) с наибольшим потреблением 
- CPU
- DISK
- MEM

<img width="903" height="297" alt="изображение" src="https://github.com/user-attachments/assets/9030a2fe-a528-4d52-9ab3-497738904e9b" />
