# portfolio1
simple bulletin board system

<?php

$filename = './review.txt';
$date     = date('Y-m-d H:i:s');
$data     = array();
$name     = '';
$comment  = '';
$err_msg  = array();

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
  if (isset($_POST['name']) === true) {
    $name = $_POST['name'];
  }
  if (isset($_POST['comment']) === true) {
  $comment = $_POST['comment'];
  }
  
  if ($name === '') {
      $err_msg[] = '名前を入力してください'; 
  } else if (mb_strlen($name) > 20) {
      $err_msg[] = '名前は20文字以内でお願いします';
  }
  
  if($comment === '') {
      $err_msg[] = 'コメントを入力してください';
  } else if (mb_strlen($comment) > 100) {
      $err_msg[] = 'コメントは100文字以内でお願いします';
  }

  if (count($err_msg) === 0) {
      $log = $name . ":\t" . $comment . "\t-" . $date .  "\n";
      if (($fp = fopen($filename, 'a')) !== FALSE) {
        if (fwrite($fp, $log) === FALSE) {
          $err_msg[] = 'ファイル書き込み失敗:  ' . $filename;
        }
        fclose($fp);
      }
      
  }
}
 
 
if (is_readable($filename) === TRUE) {
  if (($fp = fopen($filename, 'r')) !== FALSE) {
    while (($tmp = fgets($fp)) !== FALSE) {
      $data[] = htmlspecialchars($tmp, ENT_QUOTES, 'UTF-8');
    }
    $data = array_reverse($data);
    fclose($fp);
  }
} else {
  $err_msg[] = 'ファイルがありません';
}
    
?>
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>ひとこと掲示板</title>
    </head>
    <body>
        <h1>ひとこと掲示板</h1>
        <?php foreach ($err_msg as $value) { ?>
          <p><?php print $value; ?></p>
        <?php } ?>
        <form method="post">
            <p>お名前：<input type="text" name="name"></p>
            <p>ひとこと：<input type="text" name="comment"></p>
            <input type="submit" name="submit" value="送信">
        </form>
        <ul>
        <?php foreach ($data as $read) { ?>
          <li><?php print $read; ?></li>
        <?php } ?>
        </ul>
    </body>
</html>
