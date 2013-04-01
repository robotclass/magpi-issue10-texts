В корневой папке размещаются оригинальные англоязычный статьи.

Схема такая:

1. из PDF файла журнала извлекается текст, можно с сохранением переносов, создаётся файл, 
где первой цифров в формате dd - является номер страницы, где начинается эта статья, далее название статьи, расширение md.
Расширение md - означает язык форматирования текста [Markdown](http://scadahacker.com/library/Documents/Cheat_Sheets/HTML%20-%20Markdown.pdf "Краткий справочник по синаксису Markdown") в нём можно делать переносы строк - 
они будут объединены в результируем файле.

2. в подпапке соответствующей языку (ru) - создаётся идентичный файл (с английским содержимым)

3. этот файл постепенно переводится, можно переводить напрямую через веб-браузер в github - там предусмотрена
такая возможность.

Таким образом по окончанию перевода у нас будет папка ru - в которой будут переведы все тексты журнала.

После этого берём оригинал верстки в Scribus, вручную заменяется текст на каждой странице на русский. 
Сохраняя верстку и т.п. (Возможно понадобится корректировка/перевод изображений).

После этого генерируем PDF - и получаем русскоязычную версию журнала.



Подсказка по MarkDown формату:
https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet