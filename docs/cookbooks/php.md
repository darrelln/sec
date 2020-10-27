---
layout: default
title: PHP
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## PHP MySql Examples
### Query Databases
```php
<!DOCTYPE HTML>
<html>
<head>
</head>
<body>
<div>
<?php
try {
	$mysqli = new mysqli("localhost", "theseus", "iamkingtheseus");
	if($result = $mysqli->query("show databases")) {
  	    echo "Row count: {$result->num_rows}";
  	    echo "<br />";

        while($db = $result->fetch_row()) {
	        echo "Database: {$db[0]}";
	        echo "<br />";
        }
	}	
	$mysqli->close();
} catch (PDOException $e) {
	echo "Error: " . $e->getMessage();
}
?>
</div>
</body>
</html>
```

### Query Tables in Database
```php
<!DOCTYPE HTML>
<html>
<head>
</head>
<body>
<div>
<?php
try {
	$mysqli = new mysqli("localhost", "theseus", "iamkingtheseus", "Magic");
	if($result = $mysqli->query("show tables")) {
  	    echo "Row count: {$result->num_rows}";
  	    echo "<br />";

        while($table = $result->fetch_row()) {
	        echo "Table: {$table[0]}";
	        echo "<br />";
        }
	}	
	$mysqli->close();
} catch (PDOException $e) {
	echo "Error: " . $e->getMessage();
}

?>
</div>
</body>
</html>
```

### Query Rows from Table
```php
<!DOCTYPE HTML>
<html>
<head>
</head>
<body>
<div>
<?php
try {
	$mysqli = new mysqli("localhost", "theseus", "iamkingtheseus", "Magic");
	if ($result = $mysqli->query("select username, password from login")->fetch_all(MYSQLI_ASSOC)) {
		echo "Row count: {$result->num_rows}";
		echo "<br />";
		
		foreach ($result as $row) {
    	    echo "Creds: {$row[0]}:{$row[1]}";
			echo "<br />";
		}

        $result->free();
	}	
	$mysqli->close();
} catch (PDOException $e) {
	echo "Error: " . $e->getMessage();
}
?>
</div>
</body>
</html>
```