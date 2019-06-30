Biblioteka do obsługi wszelkiego rodzaju plików. System opiera się na podstawowym założeniu wgrania pliku i jego odczycie. Kilka dodatkowych metod sprawia ze jest przyjazna w urzyciu np. mechanizm zabezpieczający przed przypadkowym nadpisaniem pliku. System sam poinformuje ze taki plik w danej lokalizacji istnieje. 
Sposób przechowywania informacji o obrazach jest dowolna. Może być to zwykły file_exist bądź `mysql <https://dframeframework.com/pl/docs/database/master/query>`_. Dodatkowym autem biblioteki jest to ze pliki można zapisywać w dowolny sposób (Ftp, Local, NullAdapter) poprzez `thephpleague/flysystem <https://github.com/thephpleague/flysystem>`_ Bądź twórzyć własne adaptery. 

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

.. code-block:: php

 use League\Flysystem\Adapter\Local;
 use League\Flysystem\Cached\CachedAdapter;
 use League\Flysystem\Cached\Storage\Memory as CacheStore;
 use League\Flysystem\Filesystem;
 use Model\DatabaseDriver;

 // Create the original file store
 $localAdapter = new Local(__DIR__ . '/uploads');
 
 // Create the cache store
 $cacheStore = new CacheStore();
 
 // Decorate the adapter
 $cacheAdapter = new CachedAdapter(new Local(__DIR__ . '/public_html/cache'), $cacheStore);
 

 $config = [
     'pluginsDir' => __DIR__ . '/plugins',
     'adapters' => [
         'local' => new Filesystem($localAdapter),
         'cache' => new Filesystem($cacheAdapter), 
     ],
     'cache' => [
         'life' => 600 // in seconds
     ],
     'public_urls' => [
         'local' => ''
     ]
 ];

 $FileStorage = new \Dframe\FileStorage\Storage(new DatabaseDriver, $config);
 $FileStorage->settings([
    'stylists' => [
        'simple' => \Dframe\FileStorage\Stylist\SimpleStylist::class
    ]
 ]);
     

Przykładowy uzupełniony Driver znajdziesz tutaj `Database Driver <https://github.com/dframe/fileStorage/blob/master/examples/example1/app/Model/FileStorage/Drivers/DatabaseDriver.php>`_


Poniżej pusty Driver

.. code-block:: php
 
 namespace Model; 
 
 use Dframe\FileStorage\Drivers\DatabaseDriverInterface;
 
 class DatabaseDriver implements DatabaseDriverInterface
 {
     /**
      * @param      $adapter
      * @param      $path
      * @param bool $cache
      *
      * @return mixed
      */
     public function get($adapter, $path, $cache = false)
     {
         // TODO: Implement get() method.
     }
     
     /**
      * @param $adapter
      * @param $path
      * @param $mine
      * @param $stream
      *
      * @return mixed
      */
     public function put($adapter, $path, $mine, $stream)
     {
         // TODO: Implement put() method.
     }
     
     /**
      * @param $adapter
      * @param $originalId
      * @param $path
      * @param $mine
      * @param $stream
      *
      * @return mixed
      */
     public function cache($adapter, $originalId, $path, $mine, $stream)
     {
         // TODO: Implement cache() method.
     }
     
     /**
      * @param $adapter
      * @param $path
      *
      * @return mixed
      */
     public function drop($adapter, $path)
     {
         // TODO: Implement drop() method.
     }
     
     
     
Wgrywanie
----------
Umieszczenie pliku w lokalnym katalogu prywatnym, bez dostępu do http. Przykładowy model jest dostępny `Tutaj
<https://github.com/dframe/fileStorage/blob/master/examples/example1/app/Model/FileStorage/Drivers/DatabaseDriver.php>`_

.. code-block:: php

 if (isset($_POST['upload'])) {
 
     if (!$FileStorage->isAllowedFileType($_FILES['file'], ['jpg' => ['image/jpeg', 'image/pjpeg']])) {
         exit(json_encode(['code' => 400, 'message' => 'Uploaded file is not a valid image. Only JPG files are allowed']));
     }
 
     $put = $FileStorage->put('local', $_FILES['file']['tmp_name'], 'images/' . $_FILES['file']['name']);
     if ($put['return'] == true) {
         exit(json_encode(['code' => 200, 'message' => 'File Uploaded']));
 
     } elseif ($put['return'] == false) {
 
         //I know file exist, try put forced
         $put = $FileStorage->put('local', $_FILES['file']['tmp_name'], 'images/' . $_FILES['file']['name'], true);
         if ($put['return'] == true) {
             exit(json_encode(['code' => 207, 'message' => 'File existed and was overwritten']));
         }
 
     }
 
     exit(json_encode(['code' => 500, 'message' => 'Internal Error']));
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
