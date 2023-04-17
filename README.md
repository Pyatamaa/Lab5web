# Lab5web

pertama buat dahulu folder class yang berisi script untuk digunakan dengan konsep modularisasi

lalu buat file form-input.php

```
<?php require('header.php'); ?>

<?php
/**
* Program memanfaatkan Program 10.2 untuk membuat form inputan sederhana.
**/
include "form.php";
include "database.php";
echo "<html><head><title>Mahasiswa</title></head><body>";
$form = new Form("","Input Form");
$form->addField("txtnim", "Nim");
$form->addField("txtnama", "Nama");
$form->addField("txtalamat", "Alamat");
echo "<h3>Silahkan isi form berikut ini :</h3>";
$form->displayForm();

// Menyimpan data ke database
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $nim = $_POST['txtnim'];
    $nama = $_POST['txtnama'];
    $alamat = $_POST['txtalamat'];
    $db = new Database();
    $data = array(
        "nim" => $nim,
        "nama" => $nama,
        "alamat" => $alamat
    );
    $result = $db->insert("akmal", $data);
    if ($result === false) {
        echo "Error: Gagal menyimpan data";
    } else {
        echo "Data berhasil disimpan";
    }
}

echo "</body></html>";

?>

<?php require('footer.php'); ?>
```

kemudian buat file config.php

```
<?php
define('DB_HOST', 'localhost');
define('DB_USER', 'root');
define('DB_PASS', '');
define('DB_NAME', 'latihan1');
?>
```

setelah itu buat file database.php untuk koneksi ke database

```
<?php
class Database {
    protected $host;
    protected $user;
    protected $password;
    protected $db_name;
    protected $conn;
    
    public function __construct() {
        $this->getConfig();
        $this->conn = new mysqli($this->host, $this->user, $this->password, $this->db_name);
        if ($this->conn->connect_error) {
            die("Connection failed: " . $this->conn->connect_error);
        }
    }
    
    private function getConfig() {
        $config = include("config.php");
        $this->host = $config['host'];
        $this->user = $config['username'];
        $this->password = $config['password'];
        $this->db_name = $config['db_name'];
    }
    
    public function query($sql) {
        return $this->conn->query($sql);
    }
    
    public function get($table, $where=null) {
        if ($where) {
            $where = " WHERE ".$where;
        }
        $sql = "SELECT * FROM ".$table.$where;
        $sql = $this->conn->query($sql);
        $sql = $sql->fetch_assoc();
        return $sql;
    }
    
    public function insert($table, $data) {
        $columns = array();
        $values = array();
        if (is_array($data) && count($data) > 0) {
            foreach($data as $key => $val) {
                $columns[] = $key;
                $values[] = "'".$this->conn->real_escape_string($val)."'";
            }
            $columns = implode(",", $columns);
            $values = implode(",", $values);
            $sql = "INSERT INTO ".$table." (".$columns.") VALUES (".$values.")";
            $result = $this->conn->query($sql);
            if ($result === false) {
                die("Error: ".$this->conn->error);
            } else {
                return $result;
            }
        } else {
            return false;
        }
    }
    
    
    public function update($table, $data, $where) {
        $update_value = array(); // Ubah dari string kosong menjadi array kosong
        if (is_array($data)) {
            foreach($data as $key => $val) {
                $update_value[] = "$key='{$val}'";
            }
            $update_value = implode(",", $update_value);
        }
        $sql = "UPDATE ".$table." SET ".$update_value." WHERE ".$where;
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return true;
        } else {
            return false;
        }
    }
    
    
    public function delete($table, $filter) {
        $sql = "DELETE FROM ".$table." ".$filter;
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return true;
        } else {
            return false;
        }
    }
}
?>
```

setelah itu buat file form.php

```
<?php
/**
* Nama Class: Form
* Deskripsi: CLass untuk membuat form inputan text sederhan
**/
class Form
{
private $fields = array();
private $action;
private $submit = "Submit Form";
private $jumField = 0;
public function __construct($action, $submit)
{
$this->action = $action;
$this->submit = $submit;
}
public function displayForm()
{
echo "<form action='".$this->action."' method='POST'>";
echo '<table width="100%" border="0">';
for ($j=0; $j<count($this->fields); $j++) {
echo "<tr><td 
align='right'>".$this->fields[$j]['label']."</td>";
echo "<td><input type='text' 
name='".$this->fields[$j]['name']."'></td></tr>";
}
echo "<tr><td colspan='2'>";
echo "<input type='submit' value='".$this->submit."'></td></tr>";
echo "</table>";
}
public function addField($name, $label)
{
$this->fields [$this->jumField]['name'] = $name;
$this->fields [$this->jumField]['label'] = $label;
$this->jumField ++;
}
}
?>
```

kemudian tambahkan css dengan nama file style.css agar tampilan menjadi lebih menarik

```
/* Styling untuk body */
body {
    margin: 0;
    padding: 0;
    font-family: Arial, sans-serif;
    background-color: #217e2e;
  }
  
  /* Styling untuk container */
  .container {
      width: 80%;
      margin: 0 auto;
      text-align: center;
  }
  
  /* Styling untuk judul */
  h1 {
    margin-top: 50px;
    font-size: 36px;
    font-weight: bold;
    color: #000000;
  }
  
  /* Styling untuk tabel */
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 30px;
  }
  
  table th, table td {
    padding: 15px;
    text-align: center;
    border: 1px solid #ccc;
  }
  
  table th {
    background-color: #000000;
    color: #fff;
  }
  
  /* Styling untuk link tambah data */
  a {
    display: inline-block;
    padding: 10px 20px;
    background-color: #333;
    color: #fff;
    text-decoration: none;
    margin-top: 20px;
    margin-bottom: 20px;
    border-radius: 5px;
  }
  
  a:hover {
    background-color: #555;
  }
  
  /* Styling untuk form input */
  .input {
    margin-bottom: 20px;
    text-align: left;
  }
  
  .input label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  .input input, .input select {
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 16px;
    margin-top: 5px;
  }
  
  .input input:focus, .input select:focus {
    outline: none;
    border: 1px solid #555;
  }
  
  /* Styling untuk tombol simpan dan reset */
  .btn {
    margin-top: 20px;
  }
  
  .btn button {
    padding: 10px 20px;
    background-color: #333;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    margin-right: 10px;
  }
  
  .btn button:hover {
    background-color: #555;
  }
  
  /* Styling untuk gambar */
  img {
    max-width: 150px;
    height: auto;
    margin: 0 auto;
    display: block;
    margin-top: 10px;
    margin-bottom: 10px;
  }
  
  ```
  
  hasilnya
  
  ![Screenshot (107)](https://user-images.githubusercontent.com/92738041/232634798-93c02c6f-6a3b-43b9-b5bc-bcf18e3c185c.png)
