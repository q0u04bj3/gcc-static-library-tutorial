# 用 gcc 自製 Static Library

轉自 PTT LinuxDev

—

作者: cole945 (躂躂..) 看板: LinuxDev

標題: [心得] 用 gcc 自製 Library

時間: Sun Nov 5 04:15:45 2006

Library可分成三種，static、shared與 dynamically loaded。

- Static libraries

Static 程式庫用於靜態連結，簡單講是把一堆 object 檔用 ar(archiver) 包裝集合起來，檔名以 `.a` 結尾。優點是執行效能通常會比後兩者快，而且因為是靜態連結，所以不易發生執行時找不到library或版本錯置而無法執行的問題。缺點則是檔案較大，維護度較低；例如 library 如果發現 bug 需要更新，那麼就必須重新連結執行檔。

  - 編譯

編譯方式很簡單，先例用 `-c` 編出 object 檔，再用 ar 包起來即可。

```
// hello.c

#include <stdio.h>
void hello(){ printf(“Hello “); }
```

```
// world.c

#include <stdio.h>
void world(){ printf(“world.”); }
```

```
// mylib.h

void hello();
void world();
```
```
$ gcc -c hello.c world.c /* 編出hello.o 與world.o */
```
```
$ ar rcs libmylib.a hello.o world.o /* 包成limylib.a */
```

這樣就可以建出一個檔名為 `libmylib.a` 的檔。
輸出的檔名其實沒有硬性規定，
但如果想要配合 gcc 的 `-l` 參數來連結，
一定要以 `lib` 開頭，
中間是你要的 library 名稱，然後緊接著 `.a` 結尾。

- 使用

```
// main.c

#include “mylib.h”

int main() {

hello();

world();

}
```

使用上就像與一般的 object 檔連結沒有差別

```
$ gcc main.c libmylib.a
```

也可以配合gcc 的 `-l` 參數使用

```
$ gcc main.c -L. -lmylib
```

`-Ldir` 參數用來指定要搜尋程式庫的目錄。
`.` 表示搜尋現在所在的目錄。
通常預設會搜 /usr/lib 或 /lib 等目錄。
    
`-llibrary` 參數用來指定要連結的程式庫。
`mylib` 表示要與 mylib 進行連結。
他會搜尋 library 名稱前加 `lib` 後接 `.a` 的檔案來連結。

```
$ ./a.out

Hello world.# New Document
```
