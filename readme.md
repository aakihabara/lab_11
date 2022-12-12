<p align = "center">МИНИСТЕРСТВО НАУКИ И ВЫСШЕГО ОБРАЗОВАНИЯ<br>
РОССИЙСКОЙ ФЕДЕРАЦИИ<br>
ФЕДЕРАЛЬНОЕ ГОСУДАРСТВЕННОЕ БЮДЖЕТНОЕ<br>
ОБРАЗОВАТЕЛЬНОЕ УЧРЕЖДЕНИЕ ВЫСШЕГО ОБРАЗОВАНИЯ<br>
«САХАЛИНСКИЙ ГОСУДАРСТВЕННЫЙ УНИВЕРСИТЕТ»</p>
<br><br><br><br><br><br>
<p align = "center">Институт естественных наук и техносферной безопасности<br>Кафедра информатики<br>Коньков Никита Алексеевич</p>
<br><br><br>
<p align = "center">Лабораторная работа №11<br><b>«Регулярные выражения, JavaScript и AJAX»</b><br>01.03.02 Прикладная математика и информатика</p>
<br><br><br><br><br><br><br><br><br><br><br><br>
<p align = "right">Научный руководитель<br>
Соболев Евгений Игоревич</p>
<br><br><br>
<p align = "center">г. Южно-Сахалинск<br>2022 г.</p>
<br><br><br><br><br><br><br><br><br><br><br><br>

<h1 align = "center">Введение</h1>

<p> <b>Javascript</b> - язык программирования, который позволяет вам создать динамически обновляемый контент, управляет мультимедиа, анимирует изображения, впрочем, делает всё, что угодно. Окей, не все, что угодно, но всё равно, это удивительно, что можно достичь с помощью нескольких строк JavaScript-кода. </p>

<p><b>AJAX</b> — это аббревиатура, которая означает Asynchronous Javascript and XML. На самом деле, AJAX не является новой технологией, так как и Javascript, и XML существуют уже довольно продолжительное время, а AJAX — это синтез обозначенных технологий. AJAX чаще всего ассоцириуется с термином Web 2.0 и преподносится как новейшее Web-приложение.</p>

<h1 align = "center">Цели и задачи</h1>

<p>К базовой функциональности, следует добавить следующие возможности:</p>

<ul>
  <li>К базовой функциональности, следует добавить следующие возможности:</li>
    <ul>
        <li><a href=”” title=””>Такое</a></li>
        <li><code>Такое</code></li>
        <li><i>Такое</i></li>
        <li><strike>Такое</strike></li>
        <li><strong>Такое</strong></li>
    </ul>
  <li>Должна быть проверка на закрытие тэгов, код должен быть валидным XHTML</li>
</ul>

<p>К базовой функциональности, следует добавить следующие возможности:</p>

<ul>
    <li>Валидация вводимых данных на стороне сервера и клиента</li>
    <li>Функция предпросмотра сообщения, без перезагрузки страницы</li>
    <li>Для HTML тэгов сделать панель с кнопками ([link],,[italic],[strike],[strong])</li>
    <li>Добавление визуальных эффектов так же приветствуется </li>
</ul>

<h1 align = "center">Телефонная книга</h1>


<h2 align = "center">get.php</h2>

```php
<script src='https://www.google.com/recaptcha/api.js'></script>
<?php
if (isset($_POST['g-recaptcha-response']) && $_POST['g-recaptcha-response']) {
	$secret = '6LcO8UwjAAAAAHaDkjmyyWfsjuHifM51cCARAC4B';
	$ip = $_SERVER['REMOTE_ADDR'];
	$response = $_POST['g-recaptcha-response'];
	$rsp = file_get_contents("https://www.google.com/recaptcha/api/siteverify?secret=$secret&response=$response&remoteip=$ip");
	$arr = json_decode($rsp, TRUE);
	if ($arr['success']) {
        $db = "lab91011"; 
        $host = "localhost"; 
        $user = "root"; 
        $pass = ""; 
        $conn = new mysqli($host, $user, $pass, $db);
        if ($conn->connect_error) {
	        die("Ошибка в подключении: " . $conn->connect_error);
        }

        $userFile = null;

        function getExtension($x) {
            $res = explode(".", $x);
            return $res[count($res) - 1];
        }


        $extArray = array("txt", "png", "jpeg", "gif");

        $extension = getExtension(basename($_FILES['fl']['name']));
        

        if(!in_array($extension, $extArray)){

        } else {
            if ($extension == "txt") {
                if (filesize($_FILES['fl']['tmp_name']) <= 102400) {
                    $index = rand(1, 100);
                    $uploaddir = 'files/';
                    $fileName = basename($_FILES['fl']['name']);
                    if($fileName != ""){
                        $userFile = $uploaddir . $index . "-" . basename($_FILES['fl']['name']);
                        move_uploaded_file($_FILES['fl']['tmp_name'], $userFile);
                    }
                }
            } else {
                $index = rand(1, 100);
                $uploaddir = 'files/';
                $fileName = basename($_FILES['fl']['name']);
                
                if($fileName != ""){
                    $userFile = $uploaddir . $index . "-" . basename($_FILES['fl']['name']);
                    move_uploaded_file($_FILES['fl']['tmp_name'], $userFile);
                }
            }
        }

        $userName = $_POST['name'];
        $userURL = $_POST['homepage'];
        $userText = $_POST['text'];
        $userEmail = $_POST['email'];
        $userBrowser = $_SERVER['HTTP_USER_AGENT'];
        $userIp = $_SERVER['REMOTE_ADDR'];

        if(isset($userName) && isset($userText) && isset($userEmail) && !empty($userName) && !empty($userText) && !empty($userEmail)){
            $userName = htmlspecialchars(trim($userName));
            $userEmail = htmlspecialchars(trim($userEmail));
            $userText = htmlspecialchars(trim($userText));
            $userURL = htmlspecialchars(trim($userURL));

            $res = mysqli_query($conn, "INSERT INTO guests (name, email, homepage, text, browser, ip, fl) VALUES 
            ('$userName', '$userEmail', '$userURL', '$userText', '$userBrowser', '$userIp', '$userFile')");
            
            if($res == true) { 
                echo "<meta http-equiv='Refresh' content='0; URL=index.php'>"; 
            } else { 
                echo "Ошибка";
            }
        } else {
            echo "<meta http-equiv='Refresh' content='0; URL=index.php'>";
        }

        $conn->close();
    } else {
        echo "<meta http-equiv='Refresh' content='0; URL=index.php'>";
        }
} else {
    echo "<meta http-equiv='Refresh' content='0; URL=index.php'>";
    }
?>

```

<h2 align = "center">ShowTab.php</h2>

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        form {
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .inp {
            font-size:2vw;
            width: 8vw;
        }
        .yes {
            font-size:2vw;
            max-width: 15px;
            text-align: center;
        }
		a {
            text-decoration: none;
		}
        body {
            background-image: url('grnd.jpg');
            background-repeat: no-repeat;
            background-attachment: fixed;
            background-size: cover;
        }
        td, th, tr, table{
            border-collapse: collapse;
            border: 4px solid #615285;
            color: #615285;
            border-radius: 5px;
        }
        .box{
            margin-left: auto;
            margin-right: auto; 
            width: 20vw;
            background-color: white;
            border: 3px solid #615285;
            border-radius: 1vw;
            text-align: center;
        }
        .el{
            margin-left: auto;
            margin-right: auto;
            font-size: 2vw;
            line-height: 3vw;
            border: 1px solid #615285;
            border-radius: 1vw;
            width: 18vw;
            align-self: center;
        }
    </style>
    <link rel="stylesheet" href="css/lightbox.css" type="text/css" media="screen" />
    <script type="text/javascript" src="js/prototype.js"></script>
    <script type="text/javascript" src="js/scriptaculous.js?load=effects,builder"></script>
    <script type="text/javascript" src="js/lightbox.js"></script>
</head>
<body>
<?php
try {
    $conn = new PDO("mysql:host=localhost;dbname=lab91011", "root", "");
    $conn->setAttribute(PDO::ATTR_EMULATE_PREPARES,false);

    $page = 1;

    if (isset($_GET['page'])) {
        $page = filter_var($_GET['page'], FILTER_SANITIZE_NUMBER_INT);
    }

    $personCount = 25;

    $sqlcount = "select count(*) as tR from guests";
    $res = $conn->prepare($sqlcount);
    $res->execute();
    $row = $res->fetch();
    $tR = $row['tR'];
    $pages = ceil($tR / $personCount);

    $offset = ($page-1) * $personCount;
  
    $sql = "select * from guests limit :offset, :personCount";
  
    $res = $conn->prepare($sql);
  
    $res -> execute(['offset'=>$offset, 'personCount'=>$personCount]);
  
    echo "<table border='3' style='background-color: white;margin: auto; text-align: center; font-size: 1vw; width: 95vw;'><tr><th style='width: 3vw;'>Id</th>
    <th style='width: 10vw;'>Name</th><th style='width: 12vw;'>Email</th><th style='width: 15vw;'>Homepage</th><th style='width: 20vw;'>Text</th>
    <th style='width: 10vw;'>Ip</th><th style='width: 15vw;'>Browser</th><th style='width: 10vw;'>FileImage</th></tr>";
  
    while (($row = $res -> fetch(PDO::FETCH_ASSOC)) !== false) {
        
        echo "<tr>";
        echo "<td style='max-width: 3vw;'>" . $row["id"] . "</td>";
        echo "<td style='max-width: 10vw; word-wrap: break-word;'>" . htmlspecialchars_decode($row["name"])  . "</td>";
        echo "<td style='max-width: 12vw; word-wrap: break-word;'>" . htmlspecialchars_decode($row["email"]) . "</td>";
        echo "<td style='max-width: 15vw; word-wrap: break-word;'>" . htmlspecialchars_decode($row["homepage"]) . "</td>";
        echo "<td style='max-width: 20vw; word-wrap: break-word;'>" . htmlspecialchars_decode($row["text"]) . "</td>";
        echo "<td style='max-width: 10vw; word-wrap: break-word;'>" . htmlspecialchars_decode($row["ip"]) . "</td>";
        echo "<td style='max-width: 15vw; word-wrap: break-word;'>" . htmlspecialchars_decode($row["browser"]) . "</td>";

        $path = $row['fl'];
        $arr = explode(".", $path);

        if(count($arr) > 1){
            $ext = $arr[count($arr) - 1];
            if($ext == "txt"){
                echo "<td style='max-width: 77vw; word-wrap: break-word;'><a href=\"" . $path . "\" target=\"_blank\" class=\"btn btn-primary btn-lg active\" role=\"button\">TXT</td>";
            } else {
                echo "<td style='max-width: 77vw; word-wrap: break-word;'><img src=\"" . $path . "\" width=\"160\" height=\"120\"></a></td>";
            }
        } else {
            echo "<td style='max-width: 77vw; word-wrap: break-word;'>Пусто</td>";
        }
        echo "</tr>";
    }
  
    echo "</table>";

    echo "<br><div class='box'>";


    if ($page - 1 >= 1) {
        echo "<p class='el'><a href=".$_SERVER['PHP_SELF']."?page=".($page - 1).">Предыдущая</a></p>";
    }

    if ($page + 1 <= $pages) {
        echo "<p class='el'><a href=".$_SERVER['PHP_SELF']."?page=".($page + 1).">Следующая</a></p>";
    }


    echo "<p class='el'><a href='index.php'>Назад</a></p>";

    echo "</div>";

} catch(PDOException $e) {
    echo $e->getMessage();
}
?>
</body>
</html>
```

<h2 align = "center">index.html</h2>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="css/lightbox.css" type="text/css" media="screen" />
    <script type="text/javascript" src="js/prototype.js"></script>
    <script type="text/javascript" src="js/scriptaculous.js?load=effects,builder"></script>
    <script type="text/javascript" src="js/lightbox.js"></script>
    <title>Document</title>
    <style>
        form {
            text-align:center;
            margin:auto;
        }
        input {
            height:2vw;
        }
        input {
            font-size:1vw;
            width: 15vw;
            border-radius:5px;
        }
        
        body {
            background-image: url('grnd.jpg');
            background-repeat: no-repeat;
            background-attachment: fixed;
            background-size: cover;
            color: ghostwhite;
        }
        a {
            text-decoration: none;
		}
        .box{
            margin-left: auto;
            margin-right: auto;
            width: 20vw;
            background-color: white;
            border: 3px solid #615285;
            border-radius: 1vw;
            text-align: center;
        }
        .el{
            margin-left: auto;
            margin-right: auto;
            font-size: 2vw;
            line-height: 5vw;
            border: 1px solid #615285;
            border-radius: 1vw;
            width: 18vw;
            align-self: center;
        }
    </style>
</head>

<div class="box">
        <p class="el"><a href="AddToTab.php">Добавить</a></p>
        <p class="el"><a href="ShowTab.php">Посмотреть</a></p>
</div>
</body>
</html>
```

<h2 align = "center">AddToTab.php</h2>

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        form{
            width: 100%;
            height: 100%;
        }
        input, textarea{
            border: solid #615285;
            font-size:1vw;
            width: 15vw;
            border-radius:5px;
            color: #615285;
        }
        textarea{
            height: 3vw;
        }
        .parent {
            margin-left: auto;
            margin-right: auto;
            width: 30vw;
            height: 35vw;
            background-color: white;
            border: 3px solid #615285;
            border-radius: 1vw;
            text-align: center;
        }
        .child {
            margin-left: auto;
            margin-right: auto;
            font-size: 1vw;
            line-height: 1.5vw;
            border-radius: 1vw;
            width: 18vw;
        }
        body {
            background-image: url('grnd.jpg');
            background-repeat: no-repeat;
            background-attachment: fixed;
            background-size: cover;
            color: #615285;
            font-size: 1vw;
            position: relative;
        }
        button {
            background-color: #615285;
            border: 1px solid white;
            border-radius: 5px;
            font-size: 1.25vw;
            color: ghostwhite;
        }
        #tooltip {
            z-index: 9999;
            position: absolute;
            display: none;
            top:0px;
            left:0px;
            width: 250px;
            background-color: #fff;
            padding: 5px 10px 5px 10px;
            color: #000;
            border-radius: 5px; 
            box-shadow: 0 1px 2px #555;
        }
    </style>
</head>
<body>
<script src='https://www.google.com/recaptcha/api.js'></script>
<?php
$db = "lab91011"; 
$host = "localhost"; 
$user = "root"; 
$pass = ""; 
$conn = new mysqli($host, $user, $pass); 
if ($conn->connect_error) {
	die("Ошибка в подключении: " . $conn->connect_error);
}

?>
<form enctype="multipart/form-data" action="get.php" method="post">
<div class="parent">
    <div class="child">
        <label>Имя</label><br>
        <input type="text" name="name" size="45"><br>
        <label>Почта</label><br>
        <input id="emailinp" class="form-control" type="email" name="email" size="45"><br>
        <label>Сайт(Необязательно)</label><br>
        <input type="url" name="homepage" size="45"><br>
        <button type="button" onclick="link()">link</button>
        <button type="button" onclick="italic()">italic</button>
        <button type="button" onclick="strike()">strike</button>
        <button type="button" onclick="strong()">strong</button><br>
        <label>Текст</label><br>
        <textarea data-tooltip="Подсказка номер 1" id="textarea" name="text" class="form-control"></textarea><br>
        <div style="color:red;" id="valid"></div>
        <input type="file" name="fl" accept=".jpeg, .png, .gif, .txt"><br>
        <label>Допустимые форматы</label><br>
        <label>.jpeg, .png, .gif, .txt(Не более 100Кб)</label><br>
        <p><div class="g-recaptcha" data-sitekey="6LcO8UwjAAAAAGRaOkB5t2VulDxiFokCabyqF8YI"></div></p>
        <p><div class="text-danger" id="recaptchaError"></div></p>
        <input type="submit">

        

        <script src="http://code.jquery.com/jquery-3.1.1.min.js"></script>
        <script>

        function link(){
            document.getElementById('textarea').value +="<a href=”” title=””> </a>";
        }

        function italic(){
            document.getElementById('textarea').value +="<i> </i>";
        }

        function strike(){
            document.getElementById('textarea').value +="<strike> </strike>";
        }

        function strong(){
            document.getElementById('textarea').value +="<strong> </strong>";
        }

        $('#textarea').on('input', function() {
            if(ValidText()){
                $('#valid').empty();
            } else {
                document.getElementById('valid').innerHTML="Некоректно введенные данные";
            }
            
        });

        $('#emailinp').on('input', function() {
            if(ValidEmail()){
                emailinp.style.borderColor = 'green';
            }else{
                emailinp.style.borderColor = 'red';
            }
            
        });

        function ValidEmail() {
            let email = document.getElementById('emailinp').value;
            let res = /[a-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+/=?^_`{|}~-])*@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?/;
            return res.test(String(email).toLowerCase());
        }

        function ValidText() {
            let str = document.getElementById('textarea').value;
            str = str.replace(/\s/g, '');
            str = str.replace(/[a-zа-яё]/gi, '');
            let ch1 = 0;
            let ch2 = 0;
            for (let i = 0; i < str.length; i++) {
                if(str[i]=="<"){
                    ch1++;
                }
                if(str[i]==">"){
                    ch2++;
                }
            } 
            return ch1 % 2 == 0 && ch1==ch2;
        }
        
        $(function(){
            $("[data-tooltip]").mousemove(function (eventObject) {
                if(document.getElementById('textarea').value == ""){
                    return;
                }
                $data_tooltip = document.getElementById('textarea').value;
                $("#tooltip").html($data_tooltip)
                    .css({ 
                        "top" : eventObject.pageY + 5,
                        "left" : eventObject.pageX + 5
                    })
                    .show();
                }).mouseout(function () {
                    $("#tooltip").hide()
                        .html("")
                        .css({
                            "top" : 0,
                            "left" : 0
                });
            });
        });

    </script>
    </div>
</div>
</form>
<div id="tooltip"></div>
</body>
</html>
```

<h1 align = "center">Вывод</h1>
<p>Опираясь на материал с сайта w3school, помощь различных руководств, Я выполнил лабораторную работу и научился некоторым функциям в mySQL и не только.</p>