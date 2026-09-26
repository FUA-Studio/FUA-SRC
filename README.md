# FUA-SRC


fua src 是一门脚本语言，源文件后缀 .fs（默认）或 .fsrc，插件包后缀 .fmp。 B含：解释器内核、FSIDE 集成开发环境、FS_EDITOR 纯编辑器、插件市场（联网）


每条命令以 ; 结尾（少数例外）。注释以 | 开始到行尾。 & 可以把多条命令串接，前一条的输出可作为后一条的数据源


输出


out "内容 文本、数字等 算式不计算";      | 原样输出
out A+B --E;                            | A、B 是数字变量，算式输出计算结果；--E 表示多行时换行输出
out(一些命令);                          | 输出某条命令的运行结果


变量


set (A=1);                    | 赋值（文本、数字均可）
set (A=0+1);                  | 赋算式结果（参与运算的须是有理数）
set CHR=UTF-8;                | 设置输出字符集
make V name"A" value "1";     | 新建变量 A=1，value 缺省为 0，value 也可写算式
set TIME=2 (set CHR=UTF-8);   | 延时 2 秒执行括号中的命令


文件 / 目录



read ABCD.txt & set (A='(read ABCD.txt)') & out A --E;   | 读文件内容赋给 A
make ABC.md (PATH=C:\abcde\abc) & set (ABC.md="内容");   | 建文件并写入内容
make ABCD (PATH=C:\abcde\abc);                           | 建文件夹
make ABC.fs TO abc_abcd.exe PATH=C:\ABCD\123;            | 编译为 exe（未装 PyInstaller 时生成 .bat 启动器占位）


运行 / 系统



run "工具.exe";                 | 启动文件
run 'dir' CL=PS;                | CL=PS 识别为 PowerShell，CL=CMD（默认）为 cmd
scs myscreen;                   | 屏幕截图保存为 myscreen.png
sys shtd TIME=60;               | 延时 60 秒关机（IDE 内会弹确认框）
sys rbt TIME=30;                | 延时 30 秒重启
sys abort;                      | 取消已计划的关机/重启
sys browser_open;               | 启动默认浏览器


判断 / 循环


if A=B IF_1(out "相等";);        | 相等则执行
else IF_1 (out "不等";);         | IF_1 判断为 false 时执行
loop (out "hi";);                | 无限循环（IDE 停止按钮可终止）
loop (out I; set (I=I+1);) when (I=10) TIME=1;   | 每轮间隔 1 秒，直到 I=10


函数


set FUNCTION ABC{
    out "运行ABC";

};


ABC;


set FUNCTION ADD (123,456){
    out "和=123+456";

    
};


ADD(2,3);            | 参数不带单引号 → 先按算式计算（输出 和=5）


ADD('2','3');        | 参数带单引号 → 原样文本（输出 和=2+3）


插件


在文件开头启用插件：



INSERT SYSTEM FROM Default;              | 系统插件（自带）


INSERT Hypertext FROM EDL;               | 超文本渲染（市场下载）


INSERT link FROM EDL;                    | 链接打开（市场下载）


INSERT MADE-P FROM Default & PF=TRUE;    | 插件制作（自带）；PF=TRUE 时保存自动转 .fmp


Hypertext（HTML / Markdown 原生渲染窗口）：





make AHTML ={ <h1>标题</h1> } & out AHTML --E;


make AMD{ # Markdown **加粗** } & out AMD;


link（默认浏览器打开，https 协商失败自动降级 http，本地 IP 默认 http）：



link(example.com);


link(127.0.0.1) PORT=8008;


link(example.com) TO root/admin/abc;


MADE-P（用 fua 制作插件）：



mp ABC={ out "插件函数"; };


mp ABC(123){ out "参数:123"; };


mp ot MyPlugin.fmp PATH=C:\abcde\abc;    | 导出 .fmp 插件包



导出的 .fmp 可在 IDE「工具 → 导入 .fmp 安装」或市场安装； 其他文件中 INSERT 插件名 FROM EDL; 即可调用其中的 mp 函数。
