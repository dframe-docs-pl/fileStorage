Wgrywanie
^^^^^^^^^^

Umieszczanie pliku w lokalnym prywatnym katalogu bez dostępu przez http użyty do tego Model jest dostępny tutaj Poniższy przykład przedstawia odebranie obrazka ze strony php poprzez formularz.

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


Odczytywanie
^^^^^^^^^^

W celu odczytania obrazu możemy zrobić to na 2 sposoby. Jeśli plik był wgrywany prywatnego bez dostępu przez http musimy utworzyć kontroller który go nam z tamtąd pobierze i wyświetli. W tym celu mamy poniższy kod.


.. code-block:: php

 exit($FileStorage->renderFile('images/path/name/screenshot.jpg', 'local'));
 
Powyższy kod zwróci nam orginalny plik niezależnie czy jest to .jpg czy .pdf

Obróka Obrazka
^^^^^^^^^^

Biblioteka posiada dodatkową zaletę obróbki w locie obrazka dzięki temu ze można dopisać swój driver możemy obrabiać obrazek w dowolny sposób.

.. code-block:: php

 echo $FileStorage->image('images/path/name/screenshot.jpg')->stylist('square')->size('250x250')->display();
 
Po obróbce zostanie zwrócony link do wyrenderowanego kwadratu o wymiarach 250x250

Kasowanie
^^^^^^^^^^

Drop kasuje nam plik z podanego adaptera, automatycznie jest również wybierany adapter Cache z którego są usuwane wszystkie kopie tego pliku.

.. code-block:: php

 echo $FileStorage->drop('local', 'images/path/name/screenshot.jpg');
 
