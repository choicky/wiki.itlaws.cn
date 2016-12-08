# 为emlog 5.x添加https支持

原文在： [https://blog.vsean.net/post/196](https://blog.vsean.net/post/196)

https 支持已集成到Emlog 6.0，以下内容仅适用于Emlog 5.x用户。

在进行修改之前，请先确定服务器已正确配置https。并且备份所有文件，防止遇到兼容性问题。

1. /include/lib/option.php

请将以下内容粘贴到 get function 的 default 判断分支之前 （在Emlog 5.3.1下是第43行）

```
case 'blogurl':
    return realUrl();
    break;
```

2. /include/lib/function.base.php

请将以下内容粘贴到文件的末尾

```
/**
 * 获取当前访问的base url
 */
function realUrl() {
    static $real_url = NULL;
    
    if ($real_url !== NULL) {
        return $real_url;
    }
 
    $emlog_path = EMLOG_ROOT . DIRECTORY_SEPARATOR;
    $script_path = pathinfo($_SERVER['SCRIPT_NAME'], PATHINFO_DIRNAME);
    $script_path = str_replace('\\', '/', $script_path);
    $path_element = explode('/', $script_path);
    
    $this_match = '';
    $best_match = '';
    
    $current_deep = 0;
    $max_deep = count($path_element);
    
    while($current_deep < $max_deep) {
        $this_match = $this_match . $path_element[$current_deep] . DIRECTORY_SEPARATOR;
        
        if (substr($emlog_path, strlen($this_match) * (-1)) === $this_match) {
            $best_match = $this_match;
        }
        
        $current_deep++;
    }
    
    $best_match = str_replace(DIRECTORY_SEPARATOR, '/', $best_match);
    $real_url  = $_SERVER['HTTPS'] === 'on' ? 'https://' : 'http://';
    $real_url .= $_SERVER["SERVER_NAME"];
    $real_url .= in_array($_SERVER['SERVER_PORT'], array(80, 443)) ? '' : ':' . $_SERVER['SERVER_PORT'];
    $real_url .= $best_match;
    
    return $real_url;
}
```

3. /init.php

请用以下代码覆盖同名的define （在Emlog 5.3.1下是第39行）

    define('DYNAMIC_BLOGURL', Option::get("blogurl"));
