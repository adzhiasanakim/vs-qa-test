# vs-qa-test
Repository for Veeam Software Junior Developer in QA test.
Все задачи были выполнены на языке *C#*. Платформа *Microsoft .Net Core* 5.0.
## FirstTask
### Краткое описание
В данном проекте была реализована программа, осуществляющая копирование файлов в соответствии с конфигурационным файлом. Конфигурационный файл имеет формат *xml*. Для каждого файла в конфигурационном файле указано его имя, исходный путь и путь, по которому файл требуется скопировать.  
Пример такого *xml*-файла:  
```xml
<?xml version="1.0" encoding="utf-8" ?>
<config>
  <file
    source_path ="C:\Windows\system32"
    destination_path="C:\Program files"
    file_name="kernel32.dll"
  />
  <file
  source_path ="/var/log"
  destination_path="/etc"
  file_name="server.log"
  />
</config>
```  
### Подготовка и запуск 
Для начала необходимо создать конфигурационный файл по примеру с названием <code>CopyConfig.xml</code>. Поиск производится по пути: <code>/QAAutomationTest/FirstTask/bin/Debug/net5.0/CopyConfig.xml</code>. Путь для поиска файла, а также его название, можно изменить в переменной <code>path</code> в методе <code>Main</code>.
```cs
        static void Main(string[] args)
        {
            string path = Directory.GetCurrentDirectory() + @"\CopyConfig.xml";
            
            // изменение пути к файлу и его название
            path = "your path" + "your configuration file name"; 
            
            /*
            
            */            
        }
```
Далее необходимо запустить решение. Сообщения в консоли укажут на успешное копирование файла либо на ошибку.  
## SecondTask
### Краткое описание
Дан файл, содержащий имена файлов, алгоритм хэширования (один из <code>MD5/SHA1/SHA256</code>) и соответствующие им хэш-суммы, вычисленные по соответствующему алгоритму и указанные в файле через пробел. В данном проекте реализована программа, читающая данный файл и проверяющая целостность файлов.  
Пример файла:
```
file_01.bin md5 aaeab83fcc93cd3ab003fa8bfd8d8906
file_02.bin md5 6dc2d05c8374293fe20bc4c22c236e2e
file_03.bin md5 6dc2d05c8374293fe20bc4c22c236e2e
file_04.txt sha1 da39a3ee5e6b4b0d3255bfef95601890afd80709
```
Вызов  осуществляется через командную строку с передачей двух аргументов: путь к файлу и путь к директории с файлами.
```
<program> <path to the input file> <path to the directory containing the files to check>
```
### Подготовка и запуск
Для корректной работы программы необходимо иметь текстовый файл с содержимым по примеру из краткого описания (<code>файл алгоритм_хэширования хэш_сумма</code>). Далее нужно открыть командную строку и запустить программу с двумя аргументами (путь к файлу с информацией для проверки целостности и путь к директории с файлами). Пример:
```bash
SecondTask.exe D:\info.txt D:\Folder
```
После этого на консоли появится результат проверки. Пример результата:
```
file_01.bin OK
file_02.bin FAIL
file_03.bin NOT FOUND
file_04.txt OK
```
## ThirdTask
### Краткое описание
В рамках данной программы был разработан прототип тестовой системы, состоящей из двух тест-кейсов.
У каждого тест-кейса есть:
- Номер (<code>tc_id</code>) и название (<code>name</code>)
- Методы для подготовки (<code>prep</code>), выполнения (<code>run</code>) и завершения (<code>clean_up</code>) тестов
- Метод <code>execute</code>, который задаёт общий порядок выполнения тест-кейса и обрабатывает исключительные ситуации. 
Все этапы выполнения тест-кейса, а также исключительные ситуации, задокументированы в стандартном выводе.

Для проверки системы были реализованы два теста.  
#### Тест-кейс 1: Список файлов
- [<code>prep</code>] Если текущее системное время, заданное как целое количество секунд от начала эпохи *Unix*, не кратно двум, то необходимо прервать выполнение тест-кейса.
- [<code>run</code>] Вывести список файлов из домашней директории текущего пользователя.
- [<code>clean_up</code>] Действий не требуется.
#### Тест-кейс 2: Случайный файл  
- [<code>prep</code>] Если объем оперативной памяти машины, на которой исполняется тест, меньше одного гигабайта, то необходимо прервать выполнение тест-кейса.
- [<code>run</code>] Создать файл test размером 1024 КБ со случайным содержимым.
- [<code>clean_up</code>] Удалить файл test.

### Подготовка и запуск
Запуск данной программы не требует подготовки. В результате работы на консоли появится отчёт о выполнении по примеру (второй тест прошёл успешно, а в первом тесте произошла ошибка при подготовке: текущее системное время, заданное как целое количество секунд от начала эпохи *Unix*, оказалось кратно двум):
```
-----------------------------------
Test 1: Список файлов
-----------------------------------
Starting execution...
Preparation in the process...
Preparation failed.
-----------------------------------
Test 2: Случайный файл
-----------------------------------
Starting execution...
Preparation in the process...
Preparation was sucessfull!
Running...
Running was sucessfull!
Cleaning up...
Cleaning up was sucessfull!
Execution completed sucessfully!
```
Для подготовки собственного теста, необходимо создать экземпляр класса <code>TestCase</code>. Пример:
```cs
    TestCase testCase = new()
    {
        tc_id = 1,
        Name = "Название теста",
        prep = PrepMethod,
        run = RunMethod,
        clean_up = CleanUpMethod
    };
```
Тут свойства <code>prep</code>, <code>run</code>, <code>clean_up</code> - это экземляры делегата <code>TestMethod</code>:
```cs
public delegate void TestMethod();
```
Для запуска достаточно вызвать у созданного объекта метод <code>execute()</code>.
```cs
testCase.execute();
```
