Biblioteka do obsługi wszelkiego rodzaju plików. System opiera się na podstawowym założeniu wgrania pliku i jego odczycie. Kilka dodatkowych metod sprawia ze jest przyjazna w urzyciu np. mechanizm zabezpieczający przed przypadkowym nadpisaniem pliku. System sam poinformuje ze taki plik w danej lokalizacji istnieje. 
Sposób przechowywania informacji o obrazach jest dowolna. Może być to zwykły file_exist bądź mysql. Dodatkowym autem biblioteki jest to ze pliki można zapisywać w dowolny sposób (Ftp, Local, NullAdapter) poprzez league/flysystem Bądź twórzyć własne adaptery. 

Dframe/fileStorage jest to nakładka na powyzszy system dzięki któremu ustawiając config oraz driver jest już gotowy do użycia i może być wykorzystany w dowolnym systemie.

Wgrywanie
^^^^^^^^^

Umieszczanie pliku w lokalnym prywatnym katalogu bez dostępu przez http użyty do tego Model jest dostępny tutaj Poniższy przykład przedstawia odebranie obrazka ze strony php poprzez formularz

.. code-block:: php

 if(isset($_POST['upload'])){
    $fileStorage = new \Dframe\FileStorage\Storage($this->loadModel('FileStorage/Drivers/DatabaseDriver'));
    $put = $fileStorage->put('local', $_FILES['file']['tmp_name'], 'images/path/name.'.$extension);
    if($put['return'] == true)
       exit(json_encode(array('return' => '1', 'response' => 'File Upload OK')));
           
    exit(json_encode(array('return' => '0', 'response' => 'Error')));
 }
 
Odczytywanie
^^^^^^^^^^^^

W celu odczytania obrazu możemy zrobić to na 2 sposoby. Jeśli plik był wgrywany prywatnego bez dostępu przez http musimy utworzyć kontroller który go nam z tamtąd pobierze i wyświetli. W tym celu mamy poniższy kod.

.. code-block:: php

 exit($fileStorage->renderFile('images/path/name/screenshot.jpg', 'local'));
Powyższy kod zwróci nam orginalny plik niezależnie czy jest to .jpg czy .pdf

Obróka Obrazka
^^^^^^^^^^^^^^

Biblioteka posiada dodatkową zaletę obróbki w locie obrazka dzięki temu ze można dopisać swój driver możemy obrabiać obrazek w dowolny sposób.

.. code-block:: php

 echo $fileStorage->image('images/path/name/screenshot.jpg')->stylist('square')->size('250x250')->display();
Po obróbce zostanie zwrócony link do wyrenderowanego kwadratu o wymiarach 250x250
