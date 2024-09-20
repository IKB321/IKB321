ЗАДАНИЕ 1

настройка сети (8 пункт из 12)

Linux: (при установке)

IP адреса: 100.100.100.2/24

Добавить IP: (1 строчка пустая) 2 строка - /24 (255.255.255.0)

Шлюз по умолчанию: 100.100.100.1

DNS-серверы: 94.232.137.105

Домены поиск: пусто

«ДОПОЛНИТЕЛЬНО» - Сетевая подсистема «network manager» (можно еще галочку поставить)

Windows:

Где – в Settings (параметры) – параметры сети 

Edit IP settings

IPv4 on

IP address: 100.100.100.3

Subnet mask: 255.255.255.0

Gateway: 100.100.100.1

Preferred DNS: 94.232.137.105



ЗАДАНИЕ 2a

Открываем nano / vim, вводим: 

# «su -»

# nano backup.sh

Закидываем скрипт:

#!/bin/bash



# Функция для вывода справки

show_help() {

    echo "Использование: $0 [-c метод_сжатия] [-o имя_архива] файлы..."

    echo

    echo "Опции:"

    echo "  -c   Метод сжатия: gzip или bzip2 (по умолчанию: gzip)"

    echo "  -o   Имя выходного архива (по умолчанию: archive.tar)"

    echo "  файлы...  Список файлов и директорий для архивации"

}



# Переменные по умолчанию

compression="gzip"

output_archive="archive.tar"

log_file="archive_log.txt"



# Обработка аргументов командной строки

while getopts ":c:o:h" opt; do

    case ${opt} in

        c )

            compression=$OPTARG

            ;;

        o )

            output_archive=$OPTARG

            ;;

        h )

            show_help

            exit 0

            ;;

        \? )

            echo "Неверная опция: $OPTARG" 1>&2

            show_help

            exit 1

            ;;

        : )

            echo "Неверная опция: $OPTARG требует аргумент" 1>&2

            show_help

            exit 1

            ;;

    esac

done

shift $((OPTIND -1))



# Проверка наличия файлов и директорий для архивирования

if [ $# -eq 0 ]; then

    echo "Ошибка: не указаны файлы или директории для архивации" 1>&2

    show_help

    exit 1

fi



# Выбор метода сжатия

case $compression in

    gzip)

        tar_command="tar -czf"

        ;;

    bzip2)

        tar_command="tar -cjf"

        ;;

    *)

        echo "Ошибка: неподдерживаемый метод сжатия: $compression" 1>&2

        show_help

        exit 1

        ;;

esac



# Создание архива

$tar_command "$output_archive" "$@"

if [ $? -ne 0 ]; then

    echo "Ошибка: не удалось создать архив" 1>&2

    exit 1

fi



# Получение размера архива

archive_size=$(du -h "$output_archive" | cut -f1)



# Запись информации в лог-файл

echo "[$(date)] Создан архив: $output_archive, Размер: $archive_size, Метод: $compression" >> "$log_file"



# Вывод результата

echo "Архив успешно создан:"

echo "  Архив: $output_archive"

echo "  Размер: $archive_size"

echo "  Метод: $compression"

echo "Запись добавлена в $log_file"



exit 0



# chmod +x backup.sh

# mkdir ~/myfolder

# touch ~/myfolder/file1.txt

# touch ~/myfolder/file2.txt



Запускаем скрипт с дефолтным архивированием:

# ./backup.sh ~/myfolder



Запускаем скрипт с флагами для bzip2 и указания имени архива:

# ./backup.sh -c bzip2 -o ~/Рабочий\ стол/myfolder_backup.tar.bz2 ~/Рабочий\ стол/myfolder



archive_log.txt лежит в /root


2.	Архивирование и сжатие данных
a.	Создание скрипта на языке программирования bash для автоматического архивирования и сжатия данных в операционной системе Linux. Скрипт должен принимать на вход список файлов и папок для архивирования, выбирать метод сжатия (например, gzip или bzip2), создавать архив и вносить информацию о созданном архиве в лог-файл с указанием времени выполнения и объема сжатия.
1. nano archive_script.sh
2. Запишем внутрь
#!/bin/bash
# Лог-файл
log_file="archive_log.txt"
# Запрос метода сжатия у пользователя
echo "Выберите метод сжатия: gzip или bzip2."
read -r compression
# Проверка на допустимый метод сжатия
if [ "$compression" != "gzip" ] && [ "$compression" != "bzip2" ]; then
    echo "Ошибка: неверный метод сжатия '$compression'."
    echo "Ошибка: неверный метод сжатия '$compression'." >> $log_file
    exit 1
fi
# Запрос имени выходного архива
echo "Введите имя выходного архива (например, archive.tar.gz или archive.tar.bz2):"
read -r output
# Проверка на пустое имя выходного архива
if [ -z "$output" ]; then
    echo "Ошибка: не указано имя выходного архива."
    echo "Ошибка: не указано имя выходного архива." >> $log_file
    exit 1
fi
# Запрос списка файлов и папок для архивирования
echo "Введите список файлов и папок через пробел:"
read -r -a files
# Проверка на наличие файлов и папок для архивирования
if [ ${#files[@]} -eq 0 ]; then
    echo "Ошибка: не указан ни один файл или папка."
    echo "Ошибка: не указан ни один файл или папка." >> $log_file
    exit 1
fi
# Создание временного архива
temp_archive="temp_archive.tar"
tar -cf $temp_archive "${files[@]}"
# Сжатие архива
start_time=$(date +%s)
if [ "$compression" == "gzip" ]; then
    gzip -c $temp_archive > "$output"
elif [ "$compression" == "bzip2" ]; then
    bzip2 -c $temp_archive > "$output"
fi
end_time=$(date +%s)
# Удаление временного архива
rm $temp_archive
# Подсчет времени выполнения и объема сжатия
elapsed_time=$((end_time - start_time))
compressed_size=$(du -b "$output" | cut -f1)
# Запись информации в лог-файл
echo "Архив создан: $output" >> $log_file
echo "Время создания: $elapsed_time seconds" >> $log_file
echo "Размер файла: $compressed_size bytes" >> $log_file
echo "=============================" >> $log_file
# Вывод сообщения о завершении
echo "Архивирование завершено. Информация записана в $log_file."
3. Выдаем права: chmod +x archive_script.sh
4. Запускаем командой: ./archive_script.sh




b.	Создание скрипта на языке программирования PowerShell для автоматического создания архивов в операционной системе Windows с использованием программы 7-Zip.
Скрипт должен автоматически запаковывать файлы и папки с заданным расширением (например, *.txt) из указанной директории, сохранять архивы с уникальными именами и удалить исходные файлы после успешного создания архива.
cd \...\desktop
mkdir Scripts
Вбиваем команду:
# Укажите путь к директории для архивирования и путь к 7-Zip
$sourceDirectory = "C:\Users\KA2106_03\Desktop" #ВОТ ЭТУ СТРОКУ НАДО ПОМЕНЯТЬ НА ПУТЬ К РАБОЧЕМУ СТОЛУ НА МАШИНЕ
$sevenZipPath = "C:\Program Files\7-Zip\7z.exe"

# Получаем текущую дату и время для уникальных имен архивов
$currentDateTime = Get-Date -Format "yyyyMMdd_HHmmss"

# Ищем все файлы с расширением .txt в указанной директории
Get-ChildItem -Path $sourceDirectory -Recurse -Filter *.txt | ForEach-Object {
    $file = $_
    $archiveName = "$($sourceDirectory)\archive_$($currentDateTime)_$($file.BaseName).7z"
    
    # Создаем архив
    & $sevenZipPath a $archiveName $file.FullName

    # Проверяем, успешно ли создан архив, и удаляем исходный файл
    if (Test-Path -Path $archiveName) {
        Remove-Item -Path $file.FullName
    } else {
        Write-Host "Ошибка при создании архива для файла $($file.FullName)"
    }
}
Чтобы открыть, файлы появившиеся на рабочем столе, надо нажать на них выбрать пунктик в открывшемся окне «Открыть с помощью программы на компьютере», перейти в папку 7-zip и дважды нажать на 7zFM.
# Укажите путь к директории для архивирования и путь к 7-Zip
$sourceDirectory = "C:\Users\KA2106_03\Desktop"
$sevenZipPath = "C:\Program Files\7-Zip\7z.exe"

# Получаем расширение файлов от пользователя
$extension = Read-Host "Введите расширение файлов (например, .txt):"

# Получаем текущую дату и время для уникальных имен архивов
$currentDateTime = Get-Date -Format "yyyyMMdd_HHmmss"

# Ищем все файлы с указанным расширением в директории
$files = Get-ChildItem -Path $sourceDirectory -Recurse -Filter *$extension

# Проверяем, есть ли файлы с указанным расширением
if ($files.Count -eq 0) {
    Write-Host "Файлы с расширением $extension не найдены. Программа завершена."
    return
}

# Архивируем файлы и удаляем исходные файлы после успешного создания архива
foreach ($file in $files) {
    $archiveName = "$($sourceDirectory)\archive_$($currentDateTime)_$($file.BaseName).7z"
    
    #Создаем архив
    & $sevenZipPath a $archiveName $file.FullName

    # Проверяем, успешно ли создан архив, и удаляем исходный файл
    if (Test-Path -Path $archiveName) {
        Remove-Item -Path $file.FullName
    } else {
        Write-Host "Ошибка при создании архива для файла $($file.FullName)"
    }
}


a.	В домашнем каталоге (home) создать подкаталоги src, dst и temp; 
mkdir -p ~/src ~/dst ~/temp
b.	В каталоге temp создать файлы user.txt, root. txt и stud.txt произвольного содержания;
 
echo "Content for user.txt" > ~/temp/user.txt 
echo "Content for root.txt" > ~/temp/root.txt 
echo "Content for stud.txt" > ~/temp/stud.txt
c.	В каталог src скопировать файлы user.txt, root. txt и stud.txt, различного содержания;
 
echo "Different content for user.txt" > ~/src/user.txt
echo "Different content for root.txt" > ~/src/root.txt
echo "Different content for stud.txt" > ~/src/stud.txt
d.	В каталоге dst создать «жесткие» ссылки на все файлы из каталога src;
 
ln ~/src/user.txt ~/dst/user.txt
ln ~/src/root.txt ~/dst/root.txt
ln ~/src/stud.txt ~/dst/stud.txt
e.	В домашнем каталоге создать «мягкие» ссылки на файлы из каталога src;
  
ln -s ~/src/user.txt ~/user_symlink.txt
ln -s ~/src/root.txt ~/root_symlink.txt
ln -s ~/src/stud.txt ~/stud_symlink.txt
f.	Заархивируйте содержимое папки src/ в архив .tar.gz.
 
 
tar -czvf ~/src_backup.tar.gz -C ~/ src
g.	Распакуйте этот архив в директорию ~/backup
 
mkdir -p ~/backup
tar -xzvf ~/src_backup.tar.gz -C ~/backup
h.	Выведите названия всех файлов домашней директории, имеющих в названии .txt (подсказка: используйте команду find);
 
find ~ -name "*.txt"
i.	Переместите каталог temp в src;
 
mv ~/temp ~/src/










a.	Реализуйте собственную службу для создание бэкапов домашней директории (home), по пути /back
b.	Сервис должен выполняться автоматический каждый день в 12 часов, если это время пропущено то сразу после перезагрузки
c.	Так же учтите что старые бэкапы старше 30 дней должны удаляться 
1. Создаем файл: nano /usr/local/bin/backup_home.sh
2. В НЕМ ПРОПИСЫВАЕМ:
#!/bin/bash
# Создание директории для бэкапов, если её нет
BACKUP_DIR="/back"
mkdir -p $BACKUP_DIR
# Имя файла бэкапа
BACKUP_FILE="$BACKUP_DIR/home_backup_$(date +%Y%m%d%H%M%S).tar.gz"
# Создание бэкапа
tar -czf $BACKUP_FILE /home
# Удаление старых бэкапов старше 30 дней
find $BACKUP_DIR -type f -name "*.tar.gz" -mtime +30 -exec rm {} \;
3. Делаем скрипт исполняемым: chmod +x /usr/local/bin/backup_home.sh
4. Создаем файл: nano etc/systemd/system/backup_home.servic
5. ТАМ ПРОПИСЫВАЕМ:
[Unit]
Description=Backup Home Directory Service
After=network.target
[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup_home.sh
6. Создаем файл: nano /etc/systemd/system/backup_home.timer
7. В НЕМ ПРОПИСЫВАЕМ:
[Unit]
Description=Runs backup_home.service daily at 12:00
[Timer]
OnCalendar=*-*-* 12:00:00
Persistent=true
[Install]
WantedBy=timers.target
8. Перезагрузим systemd для применения изменений: systemctl daemon-reload
9. Активируйте таймер:
systemctl enable backup_home.timer
10. Запустите таймер:
systemctl start backup_home.timer
11. Проверьте статус таймера:
systemctl status backup_home.timer
12. Проверьте логи службы:
journalctl -u backup_home.service
13. Для ручного запуска службы (например, для тестирования):
systemctl start backup_home.service
14. И проверим статус: 
systemctl status backup_home.service





