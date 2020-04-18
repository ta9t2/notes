---
layout: default
title: Webページ経由のPythonスクリプト実行方法
---

# Webページ経由のPythonスクリプト実行方法

2019-04-30

PythonをCGIとして実行する方法ばかり検索でヒットするのでそれ以外の方法。PHPから直接pythonコマンドを実行する。

## 仕組み

- PHPの `exec` 関数で直接pythonコマンドを実行する。
- `move_uploaded_file`  関数でクライアント側からファイルを受け渡せばサーバ側で処理することも可能。

## コード

### index.php

```php
<!doctype html>
<html lang="ja">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css"
        integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS" crossorigin="anonymous">
    <title>Sample</title>
</head>
<body>
    <div class="container-fluid">
        <form class="form-horizontal" method="post" action="#">
            <div class="input-group">
                <div class="input-group-prepend">
                    <span class="input-group-text">Text</span>
                </div>
                <input type="text" class="form-control" name="srctxt" value="testtesttest">
                <input class="btn btn-outline-primary" type="submit" value="Execute">
            </div>
        </form>
    </div>
    <div class="container-fluid">
        Result:
        <?php
            $text = 'empty';
            if(isset($_POST['srctxt'])) {
                $arg = $_POST['srctxt'];
                exec("python python.py $arg", $output, $return_var);
                $text = $output[0];
            }
            echo $text;
        ?>
    </div>
</body>
</html>
```

### python.py

```python
# -*- coding: utf-8 -*-
import sys
print(sys.argv[1])
```

## 動作画面

![demo](https://user-images.githubusercontent.com/48476117/79638469-7089bb80-81c0-11ea-8480-3b1da230f553.gif)

## 参考

- [PHP: exec - Manual](https://www.php.net/manual/ja/function.exec.php)
- [PHP: move_uploaded_file - Manual](https://www.php.net/manual/ja/function.move-uploaded-file.php)

