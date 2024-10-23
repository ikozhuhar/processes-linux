### Process

### Что такое процесс?

Процесс - это выполнение пассивных инструкций компьютерной программы. Другими словами можно сказать, что процесс - это выполняющаяся программа и все её элементы (адресное пространство, глобальные переменные, регистры)

Программа - это пассивная последовательность инструкций, а процесс - это непостредственное выполнение этих инструкций. Еще можно сказать, что процесс - это работающая в данный момент программа.

PID (Process ID), PPID (Parent Process ID)

ls -la /proc/<pid> - кто является владельцем каталога, тот и является владельцем процесса.



### Системные вызовы для управления процессами

fork() - Создание процесса. Стартует в одном процессе, завершается в двух. Родителю возвращает PID ребенка, ребенку возвращает код 0.
exec() - Загрузка (запуск) программы в процесс. Работает в существующем процессе. Заменяет программу процесса на ту, что была передана exec() в качестве аргумента. exec() - семейство системных вызовов (execl, execle, execlp, execcv, execve, execvp)
exit() - Приводит к обычному завершению программы. exit() - не завершает процесс. После exit() все ресурсы занимаемые процессом освобождаются и появляется переменная status будет доступна родительскому процессу (status - код возврата). Благодаря этой переменной можно понять были ли ошибки в процессе работы программы или нет. Эту переменную может прочитать только родительский процесс.
wait() - Считывание кода возврата дочернего процесса. После получения кода возврата и вызова wait() дочерний процесс полностью исчезает из системы.

# Картинка - жизненный цикл процесса в Linux



### Состояние процессов

| Статус | Описание |
| ------ | -------- |
| R | Выполняется в данный момент |
| **S** | Процесс ожидает |
| **D** | Процесс ожидает ввода-вывода. Что делать? Выяcнять с помощью strace либо reboot |
| **T** | Процесс остановлен |
| **Z** | Процесс зомби |
| **I** | Многопотоковый процесс |

**Сирота** - процесс оставшийся без родителя, который будет мгновенно усыновлен процессом init. Состояние очень кратковременное увидеть которое невозможно. Усыновляется для того, чтобы получить вызов wait() от родителя, иначе он будет вечным.

**Зомби** - Состояние между exit() и wait(). Ресурсов не потребляет, убить невозомжно. Такое возможно в случае если родитель не "следит" за своими детьми. Для того, чтобы завершить процесс Зомби его нужно сделать сиротой убив родителя. После этого процесс init усыновит и отправит вызов wait(). После этого процесс полностью исчезает из системы.



#### Демон

Процесс работающий в фоновом режиме без прямого взаимодействия с пользователем. Например, sshd, httpd, ftpd и т.д. - это программы работающие в режиме демона (последняя буква в названии "d" означает daemon). Название произошло в честь демона Максвелла.

### Практика
```
ps -efl
ps -efl | head -3
ps -efl | grep ubuntu
```
```
/proc /dev /sys
```

ls -la /proc/<pid> - кто является владельцем каталога, тот и является владельцем процесса.

Например, если нужно увидить информацию о лог файлах того или иного процесса, это можно сделать из каталога /proc следующим образом:
```
1. ps -efl | grep nginx
2. sudo ls -l /proc/2976/fd
3. sudo cat /proc/2976/stat

* 2976 -  это PID процесса
```

### Заготовка для дом работы
```
lsof | grep var
sudo ls -l /proc/*/fd | grep "\/var\/cache\/fontconfig\/f831f3c"
for i in {1..65535}; do ls -l /proc/$i/fd 2> /dev/null | grep "\/var\/cache\/fontconfig\/f831f3c" && echo $i; done
cat /proc/*/maps | grep "libpostfix-util.so.1.0.1"
```

```
strace ls
```

 Нужно написать перебор каталогов состоящих из цифр в каталоге /proc
