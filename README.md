# Lab10Web: Implementasi Modularisasi dengan PHP OOP

## Nama : Alfarizki Aprilia putri 

## Nim : 312410455

## Kelas : TI.24.A5

## Deskripsi
Pada praktikum kali ini, saya Akan mengimplementasikan konsep **modularisasi** dalam pengembangan aplikasi menggunakan PHP **Object-Oriented Programming (OOP)**. Tujuan dari praktikum ini adalah untuk membuat program yang lebih terstruktur, mudah dipelihara, dan lebih efisien dengan menggunakan konsep modularisasi melalui penggunaan **class library** untuk form inputan dan koneksi database.

## Tujuan Praktikum
1. Memahami konsep dasar OOP dalam PHP.
2. Memahami penggunaan class dan object untuk modularisasi kode.
3. Menerapkan konsep modularisasi pada aplikasi menggunakan class library untuk form dan koneksi database.

## Langkah-Langkah yang Dikerjakan

### 1. **Membuat Class Library untuk Form**
Saya membuat sebuah class `Form` yang digunakan untuk membuat form inputan dinamis. Class ini menyimpan informasi mengenai field form, dan memungkinkan penambahan field secara dinamis serta menampilkan form tersebut di halaman web.

#### File `form.php`
```php
class Form {
    private $fields = array();
    private $action;
    private $submit = "Submit Form";
    private $jumField = 0;

    public function __construct($action, $submit) {
        $this->action = $action;
        $this->submit = $submit;
    }

    public function displayForm() {
        echo "<form action='".$this->action."' method='POST'>";
        echo '<table width="100%" border="0">';
        for ($j=0; $j<count($this->fields); $j++) {
            echo "<tr><td align='right'>".$this->fields[$j]['label']."</td>";
            echo "<td><input type='text' name='".$this->fields[$j]['name']."'></td></tr>";
        }
        echo "<tr><td colspan='2'>";
        echo "<input type='submit' value='".$this->submit."'></td></tr>";
        echo "</table>";
    }

    public function addField($name, $label) {
        $this->fields[$this->jumField]['name'] = $name;
        $this->fields[$this->jumField]['label'] = $label;
        $this->jumField++;
    }
}

```

### 2. Membuat Class Library untuk Database Connection

Class Database yang saya buat memungkinkan aplikasi untuk terhubung dengan database, melakukan query, serta melakukan operasi dasar seperti mengambil data, menambah data, memperbarui data, dan menghapus data.

File `database.php`

```php
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
        include_once("config.php");
        $this->host = $config['host'];
        $this->user = $config['username'];
        $this->password = $config['password'];
        $this->db_name = $config['db_name'];
    }

    public function query($sql) {
        return $this->conn->query($sql);
    }

    public function get($table, $where = null) {
        if ($where) {
            $where = " WHERE ".$where;
        }
        $sql = "SELECT * FROM ".$table.$where;
        $sql = $this->conn->query($sql);
        return $sql->fetch_assoc();
    }

    public function insert($table, $data) {
        if (is_array($data)) {
            foreach ($data as $key => $val) {
                $column[] = $key;
                $value[] = "'{$val}'";
            }
            $columns = implode(",", $column);
            $values = implode(",", $value);
        }
        $sql = "INSERT INTO ".$table." (".$columns.") VALUES (".$values.")";
        return $this->conn->query($sql);
    }
}

```

### 3. Menggunakan Class Library dalam Program Utama

Setelah membuat class `Form` dan `Database`, saya mengintegrasikan keduanya dalam file utama `form_input.php`. Program ini memungkinkan pengguna untuk mengisi form, dan data yang diinputkan akan disimpan ke dalam database.

File `form_input.php`

```php

<?php
include "form.php";
include "database.php";

echo "<html><head><title>Form Input Mahasiswa</title></head><body>";
$form = new Form("","Input Data Mahasiswa");
$form->addField("txtnim", "Nim");
$form->addField("txtnama", "Nama");
$form->addField("txtalamat", "Alamat");
echo "<h3>Silahkan isi form berikut ini :</h3>";
$form->displayForm();
echo "</body></html>";

if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $db = new Database();
    $data = [
        'nim' => $_POST['txtnim'],
        'nama' => $_POST['txtnama'],
        'alamat' => $_POST['txtalamat']
    ];
    $db->insert('mahasiswa', $data);
    echo "Data berhasil disimpan!";
}
?>

```

### 4. Konfigurasi Database

Untuk menghubungkan ke database, saya membuat file `config.php` yang berisi informasi konfigurasi database yang digunakan oleh aplikasi.

File `config.php`

```php

<?php
$config = [
    'host' => 'localhost',
    'username' => 'root',
    'password' => '',
    'db_name' => 'db_mahasiswa'
];
?>

```
