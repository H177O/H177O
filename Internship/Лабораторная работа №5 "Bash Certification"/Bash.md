# Лабораторная работа №5 "Bash Certification"
```
#!/bin/bash

# Путь к исходному шаблону грамоты
document="грамота.docx"
# Путь к файлу со списком учеников
students_list="ФИО.txt"

# Чтение файла со списком учеников (по строчно)
(tr -d '\r'| while IFS= read -r student_name
do
        # Создание временной директории для работы с документом
        tmp_dir="$(mktemp -d)"

        # Создание копии документа для текущего ученика
        cp "$document" "$tmp_dir/$student_name.docx"

        # Разархивирование файла
        unzip -q "$tmp_dir/$student_name.docx" -d "$tmp_dir/$student_name"

        # Замена ФИО на имя ученика (хранится в document.xml)
        sed -i "s/ФИО/$student_name/g" "$tmp_dir/$student_name/word/document.xml"

        # Архивирование обратно в .docx
        pushd "$tmp_dir/$student_name" > /dev/null
        zip -qr "$tmp_dir/$student_name.docx" *
        popd > /dev/null

        # Перемещение полученного документа в исходную директорию
        cp "$tmp_dir/$student_name.docx" "./"

        # Удаление временной директории
        rm -rf "$tmp_dir"
done) < "$students_list"
```
Этот скрипт меняет шаблон `ФИО` в документе `грамота.docx` на текст, который содержится в файле `ФИО.txt`. При изменении документа он создаёт его копию в ту директорию, откуда был запущен скрипт.
