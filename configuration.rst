Biblioteka do obsługi wszelkiego rodzaju plików. System opiera się na podstawowym założeniu wgrania pliku i jego odczycie. Kilka dodatkowych metod sprawia ze jest przyjazna w urzyciu np. mechanizm zabezpieczający przed przypadkowym nadpisaniem pliku. System sam poinformuje ze taki plik w danej lokalizacji istnieje. 
Sposób przechowywania informacji o obrazach jest dowolna. Może być to zwykły file_exist bądź mysql. Dodatkowym autem biblioteki jest to ze pliki można zapisywać w dowolny sposób (Ftp, Local, NullAdapter) poprzez league/flysystem Bądź twórzyć własne adaptery. 

Dframe/fileStorage jest to nakładka na powyzszy system dzięki któremu ustawiając config oraz driver jest już gotowy do użycia i może być wykorzystany w dowolnym systemie.

Instalacja
----------

Z poziomu konsoli bash wykonaj polecenie composera*

.. code-block:: bash

 $ composer require dframe/fileftorage

Albo pobierz ręcznie https://github.com/dframe/fileStorage/releases


Konfiguracja
----------

.. code-block:: php

     $FileStorage = new \Dframe\FileStorage\Storage($this->loadModel('FileStorage/Drivers/DatabaseDriver'));
     $FileStorage->settings([
        'stylists' => [
            'Orginal' => \Libs\Plugins\FileStorage\Stylist\OrginalStylist::class,
            'Real' => \Libs\Plugins\FileStorage\Stylist\RealStylist::class,
            'RectStylist' => \Libs\Plugins\FileStorage\Stylist\RectStylist::class,
            'SquareStylist' => \Libs\Plugins\FileStorage\Stylist\SquareStylist::class
        ]
     ]);
     

Wgrywanie
----------
Umieszczenie pliku w lokalnym katalogu prywatnym, bez dostępu do http. Przykładowy model jest dostępny `Tutaj
<https://github.com/dframe/fileStorage/blob/master/examples/example1/app/Model/FileStorage/Drivers/DatabaseDriver.php>`_

.. code-block:: php

 if (isset($_POST['upload'])) {
 
     $FileStorage = new \Dframe\FileStorage\Storage($this->loadModel('FileStorage/Drivers/DatabaseDriver'));
     $put = $FileStorage->put('local', $_FILES['file']['tmp_name'], 'images/path/name.'.$extension);
     if ($put['return'] == true) { 
         exit(json_encode(array('return' => '1', 'response' => 'File Upload OK')));
         
     } elseif($put['return'] == false) {
    
         //I know file exist, try put forced
         $put = $FileStorage->put('local', $_FILES['file']['tmp_name'], 'images/path/name.'.$extension, true);
         if ($put['return'] == true) {
             exit(json_encode(array('return' => '1', 'response' => 'File Upload forced method')));
         } 
         
     }
           
    exit(json_encode(array('return' => '0', 'response' => 'Error')));
 }
 
Czytanie
----------

Aby odczytać obraz, możemy zrobić to na dwa sposoby. Jeśli plik został przesłany prywatnie, bez dostępu HTTP, musimy stworzyć kontroler, który pobierze go i pokaże. W tym celu mamy poniższy kod.

.. code-block:: php

 exit($FileStorage->renderFile('images/path/name/screenshot.jpg', 'local'));
 
 
This code will return the original file to us, no matter if it's .jpg or .pdf

 
Image Processing
----------

Biblioteka ma dodatkową funkcję przetwarzania obrazu w czasie rzeczywistym, dzięki możliwości dodania własnego sterownika i możliwości przetwarzania naszego obrazu w dowolny sposób.


.. code-block:: php

 echo $FileStorage->image('images/path/name/screenshot.jpg')->stylist('square')->size('250x250')->display();
 
 Po przetworzeniu zostanie zwrócony link do renderowanego obrazu o rozmiarze 250x250.
 
 Return array

.. code-block:: php

 echo $FileStorage->image('images/path/name/screenshot.jpg')->stylist('square')->size('250x250')->get();
