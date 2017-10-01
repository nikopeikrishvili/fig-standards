ავტოჩატვირთვის სტანდარტები
====================

> **უარყოფილი** - 2014-10-21 ში PSR-0 მოინიშნა როგორც უარყოფილი. [PSR-4] არის ალტერნატივა რომელსაც ჩვენ ვუწევთ რეკომენდაციას.

[PSR-4]: http://www.php-fig.org/psr/psr-4/

ქვევით მოყვანილი აუცილებელი პირობები რომელიც უნდა იყოს გათვალისწინებული
ავტოჩატვირთვის თავსებადობისთვის

Mandatory
---------

* Class ს ან Namespace უნდა ქონდეს დაცული შემდეგი სტრუქტურა `\<Vendor Name>\(<Namespace>\)*<Class Name>`
* ყოველ namespace ს უნდა ქონდეს ზემდგომი namespace ("Vendor Name").
* ყოველ namespace ს შეიძლება ქონდეს განუსაზღვრელი რაოდენობის ქვე-namespace ები.
* ყოველი namespace ის სეპარატორი ითვლება `დირექტორიის სეპარატორი` როდესაც ცდება ჩატვირთვა ფაილთა სისტემიდან.
* ყოველი `_` სიმბოლო  class ის სახელში კონვერტირდება
  `დირექტორიის სეპარატორი` - ად. სიმბოლო `_` - ს არ აქვს რაიმე დანიშნულება namespace ში
* namespace და class სუფიქსი უნდა იყოს `.php` როდესაც იტვირთებიან ფაილთა სისტემიდან.
* Avendor, namespaces, and class სახელში შეიძლება იყოს კომბინირებულები მაღალი და დაბალი რეგისტრების ასოებისგან.

მაგალითები
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

ქვედა ტირეები `_`  Namespaces სა და Class ებში
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

სტანდარტებს რომლებსაც ვაყალიბებთ აქ არიან დომინირების ყველაზე დაბალ საფეხურზე
იმისთვის რომ კოდი იყოს თავსებადი ავტოჩატვირთვასთან. თქვენ შეგიძლიათ გატესტოთ
თქვენი კოდის თავსებადობა ამ სტანდართთან SplClassLoader ის გამოყენებით 
რომელსაც შეუძლია klassების ჩატვირთვა PHP ის 5.3 ვერსიაზე.

მაგალითის იმპლემენტაცია
----------------------

ქვევით მოყვანილი ფუნქციონალის დემონსტრაცია რომელიც იცავს მოცემულ სტანდარტს.

~~~php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
~~~

SplClassLoader - ის იმპლემენტაცია
-----------------------------

მოცამული gist არის მარტივი იმპლემენტაცია SplClassLoader რომელსაც შეუძლია ჩატვირთოს 
თქვენი კლასები მოცემული სტანდარტების დაცვით. ამ ეტაპზე ეს არის რეკომენდირებული ხერხი
PHP class ების ჩატვირთვისთვის 5.4 ვერსიაში

* [http://gist.github.com/221634](http://gist.github.com/221634)

