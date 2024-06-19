# 1

```
<?php 
$file_name = $_GET['file_name'];
$file_path = '/var/www/html/images/' . $file_name; 
if (file_exists($file_path)) {
    header('Content-Type: image/png');
    readfile($file_path);
}
else { // Image file not found
    echo " 404 Not Found";
}
```

-> /loadImage.php?file_name=/../../../../../../etc/passwd


# 2
<?php 
$file = $_GET['file'];
if (strpos($file, "..") !== false)
    die("Hack detected");
if (file_exists($file)) {
    header('Content-Type: image/png');
    readfile($file);
}
else { // Image file not found
    echo " 404 Not Found";
}?>


-> loadImage.php?file=/etc/passwd


# 3

```
<?php

    // Create store place for each user (we place this in /var/www/html/upload for easily handle)
    session_start();
    if (!isset($_SESSION['dir'])) {

        $_SESSION['dir'] = '/var/www/html/upload/' . bin2hex(random_bytes(16));
    }
    $dir = $_SESSION['dir'];

    if ( !file_exists($dir) )
        mkdir($dir);

    if(isset($_FILES["files"]) && $_POST['album'] !="" ) {
        try {

            //Create Album
            $album = $dir . "/" . strtolower($_POST['album']);
            if ( !file_exists($album))
                mkdir($album);

            //Count Files
            $files = $_FILES['files'];
            $count = count($files["name"]);
            
            // Save files to user's directory
            for ($i = 0; $i < $count; $i++) {
                
                $newFile = $album . "/" . $files["name"][$i];

                move_uploaded_file($files["tmp_name"][$i], $newFile);
            }

       } catch(Exception $e) {
            $error = $e->getMessage();
         }
    }
?>

```

Apache 
```
# CHANGELOG: disable execution of php code in upload folder and safely return content-type
<Directory "/var/www/html/upload/">
        AllowOverride None
        Require all granted

        <FilesMatch ".*">
                SetHandler None
        </FilesMatch>

        Header set Content-Type application/octet-stream

        <FilesMatch ".+\.jpg$">
                Header set Content-Type image/jpeg
        </FilesMatch>
        <FilesMatch ".+\.png$">
                Header set Content-Type image/png
        </FilesMatch>
        <FilesMatch ".+\.(html|txt|php)">
                Header set Content-Type text/plain
        </FilesMatch>
</Directory>


```

-> Chèn ../.. vào tên folder, upload shell

# 4
PHP có khả năng thực thi các file có phần mở rộng không phải là .php thông qua một số cách khác nhau, nhưng điều này không khuyến khích vì lý do bảo mật và quản lý mã nguồn. Dưới đây là một số cách mà PHP có thể thực thi các file với phần mở rộng khác:

1. Đổi tên file với phần mở rộng .php
Một cách đơn giản là đổi tên file thành một đuôi mở rộng mà PHP hiểu, ví dụ .php:

2. Sử dụng include hoặc require
PHP có thể bao gồm và thực thi mã từ bất kỳ file nào, bất kể phần mở rộng của file là gì, miễn là file đó chứa mã PHP hợp lệ. Ví dụ:
<?php
include 'script.txt';  // script.txt chứa mã PHP
?>

3. Sử dụng exec hoặc shell_exec
PHP có thể gọi các lệnh hệ thống để thực thi file với phần mở rộng khác. Tuy nhiên, cách này rất nguy hiểm và dễ bị tấn công nếu không kiểm tra kỹ lưỡng đầu vào.
<?php
$output = shell_exec('php script.txt');
echo $output;
?>

4. Sử dụng eval (Không khuyến khích)
Như đã đề cập trước đây, eval() có thể thực thi mã từ một chuỗi. Dùng file_get_contents() để đọc nội dung file thành chuỗi và truyền vào eval():
<?php
$code = file_get_contents('script.txt');
eval($code);
?>

5. Cấu hình Apache/Nginx để xử lý phần mở rộng khác như PHP
Bạn có thể cấu hình server web như Apache hoặc Nginx để xử lý các file có phần mở rộng khác như PHP.

Apache
Thêm dòng sau vào file .htaccess:
AddType application/x-httpd-php .txt

Nginx
Thêm dòng sau vào file cấu hình Nginx:
nginx
Copy
location ~ \.txt$ {
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}

Mã nguồn :

![image](https://github.com/anpolishirley/CBJS/assets/139233595/8894a5f8-d027-4b5b-99b8-1e137eeca619)

-> tải shell vào file ảnh, chạy file ở game.php



