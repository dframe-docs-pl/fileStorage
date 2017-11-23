WhereChunk
^^^^^^^^^^

Tak zwane kawałki, Pomocne do przeszukiwania/filtrowania danych w bazie. Gdy tworzymy zapytanie do bazy dorzucamy różne ograniczenia WHERE za pomocą chunków możemy prościej i mniejszą ilością kodu dodawać bądź usuwać parametry doklejane do WHERE

.. code-block:: php

 namespace Controller;
 use Dframe\Config;
 use Dframe\Database\WhereChunk;
 use Dframe\Database\WhereStringChunk;
 
 class UserController extends \Controller\Controller
 {
 
     public function index() {
         $userModel = $this->loadModel('User');
         $view = $this->loadView('Index');
 
         $order = array('user.id', 'ASC');
         $where = array();
         if(isset($_POST['search']['username']))
             $where[] = new WhereChunk('`users`.`username`', '%'.$_POST['search']['username'].'%', 'LIKE');
      
         $users = $userModel->getUsers($where, $order[0], $order[1], $_POST['resourceId']);
         exit($view->renderJSON(array('users' => $users)));
     }

.. code-block:: php

 namespace Model;
 
 class UserModel extends \Model\Model
 {
     public function resources($whereObject, $order = 'id', $sort = 'DESC'){
 
         $query = $this->baseClass->db->prepareQuery('SELECT * FROM user');        
         $query->prepareWhere($whereObject);
         $query->prepareOrder($order, $sort);
 
         $results = $this->baseClass->db->pdoQuery($query->getQuery(), $query->getParams())->results();
 
         return $this->methodResult(true, array('data' => $results));
     }

W przypadku wywołania $_POST do podstawowego zapytania zostanie doklejony warunek. Wszystkie parametry automatycznie są bindowane do PDO więc nie musimy już oto matwić.

WhereStringChunk
^^^^^^^^^^^^^^^^

Ciekawszą i częściej w praktyce wykorzystywaną klasą jest WhereStringChunk daje ona nam dużo większne możliwości niż zwykłe WhereChunk.

.. code-block:: php

 $where[] = new \Dframe\Database\WhereStringChunk('col_id > ?', array('1'));
