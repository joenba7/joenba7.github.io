---
title: Simple directory listing script
date: 2008-03-27
authors:
    - jorge
categories:
    - Snippets
---
```
<?php

/*
 *   Dili is free software; you can redistribute it and/or modify
 *   it under the terms of the GNU General Public License as published by
 *   the Free Software Foundation; either version 2 of the License, or
 *   (at your option) any later version.
 *
 *   Dili is distributed in the hope that it will be useful,
 *   but WITHOUT ANY WARRANTY; without even the implied warranty of
 *   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 *   GNU General Public License for more details.
 *
 *   You should have received a copy of the GNU General Public License
 *   along with Dili; if not, write to the Free Software
 *   Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA
 *
********************************************************************************
 *  
 * This is the Dili (DIrectory LIsting) script v0.0.1.
 * Place the index.php in the directory you want listed, simple as that. :)
 *  
 * Jorge Barrera Grandon 
 * Atlantiscrew 
 *
 */

$title = basename(dirname(__FILE__)).'/';

        echo '';
        echo '';
        echo '';
        echo ''.$title.'';
        echo '';
        echo '';
        echo '';
        echo 'Directory listing of '.$title.'';

        $dir = opendir(".");

        while(FALSE !== ($file = readdir($dir))) {
            if($file != "." && $file != ".." && $file != "index.php") {
                $myFiles[] = "$file";
            }
        }

        closedir($dir);
        sort($myFiles);
        reset($myFiles);

        foreach($myFiles as $value) {
            if (is_dir($value)) {
                echo '[d] - '.$value.'/';
        } else {
                echo '[f] - '.$value.'';
            }
        }

        echo '';
        echo '';
            echo '';
                echo '';
            echo '';
            echo '';
                echo '';
            echo '';
        echo '';
        echo '';
        echo '';
?>
```

