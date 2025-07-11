## tee命令
Linux tee命令用于读取标准输入的数据，并将其内容输出成文件。(默认是覆盖文件, 追加模式下使用-a参数)
free -h|tee -a mem.txt 将free -h命令的输出内容追加到mem.txt文件中。

## cat cat命令用于连接文件并打印到标准输出设备上。
cat file1 file2 将file1和file2的内容打印到标准输出设备上。
cat >>file1 <<EOF 将输入的内容追加到file1文件中。
cat >file1 <<EOF 将输入的内容覆盖file1文件中。