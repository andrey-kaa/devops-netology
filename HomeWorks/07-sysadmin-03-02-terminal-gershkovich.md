# Домашнее задание к занятию "3.2. Работа в терминале, лекция 2"

>1. Какого типа команда `cd`? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.  

Тип - shell builtin  
- Программа одна из наиболее используемых, поэтому выгодно сделать её встроенной     
- Данная программа меняет у вызываемого его процесса атрибут - текущую рабочую директорию, если бы она была не встроенная, то порождалась как внешний процесс и наследовала бы атрибуты вызываемого процесса (например интерпретатора bash), атрибуты которые по сути ей не нужны и пыталась изменить данных атрибут у родительского процесса.  


>2.Какая альтернатива без pipe команде `grep <some_string> | wc -l`? `man grep` поможет в ответе на этот вопрос.  

grep -c <some_string> <file_name>  

>3. Какой процесс с PID `1` является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?  

**Процесс инициализации INIT**  
**$ps -fp 1**  
_/sbin/init_ 

>4. Как будет выглядеть команда, которая перенаправит вывод stderr `ls` на другую сессию терминала?  

На 1 терминале: # **tty**  
/dev/pts/0  
На 2 терминале: # **tty**  
/dev/pts/5  
На 1 терминале: #**ls /no_folder 2> /dev/pts/5**  


>5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.  

**grep -c string  < /test/file_in > file_out**

>6. Получится ли вывести находясь в графическом режиме данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?  

Да, находясь в /dev/pts3 под root или тем же пользователем (как в GUI так и через SSH):  
echo HELLO > /dev/tty3  
Вижу переданные данные в /dev/tty3  


>7. Выполните команду `bash 5>&1`. К чему она приведет? Что будет, если вы выполните `echo netology > /proc/$$/fd/5`? Почему так происходит?    

Появится новый 5-й файловый дескриптор у процесса bash с перенаправлением в стандартный поток вывода.      

>Что будет, если вы выполните `echo netology > /proc/$$/fd/5`?       

На консоль выведется «netology»  
  
>Почему так происходит?  
  
Так как 5-й файловый дескриптор bash указывает на стандартный поток вывода. Если поменять bash 5>/test/file, то уже это пойдет в файл.    



>8. Получится ли в качестве входного потока для pipe использовать только stderr команды? Используя знание из предудщего вопроса предложите способ как это сделать поменяв потоки ввода-вывода местами.  

Поток ошибок перенаправляем в стандартный поток вывода, а стандартный поток вывода куда-то ещё, например в /dev/null  
**lsof /root 2>&1 1>/test/out | less**



>9. Что выведет команда `/proc/$$/environ`? Как еще можно получить аналогичный по содержанию вывод?  

Файл содержит информацию об окружении процесса  
**#(cat /proc/$$/environ; echo) | tr "\000" "\n"**  
**#cat /proc/$$/environ | strings**  

**#env или printenv – выведет текущее окружение, которое соответствует bash.**



>10. Используя `man`, опишите что доступно по адресам `/proc/<PID>/cmdline`, `/proc/<PID>/exec`.  

**/proc/<PID>/cmdline**  - командную строку запуска процесса( например для init: /sbin/init splash). Исключение: процесс полностью ушел в swap, или стал зомби  
**/proc/<PID>/exe** -символьная ссылка на исполняемый файл процесса  



>11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью `/proc/cpuinfo`.  

1-ю версию.

**#cat '/proc/cpuinfo' |xargs -n 1 | grep sse | sort -u**  
_sse  
sse2  
sse4_1  
sse4_2  
ssse3_  
