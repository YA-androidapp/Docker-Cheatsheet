FROM ubuntu

# シェル形式
#   既定では、 `/bin/sh -c`(Linux) または `cmd /S /C`(Windows)
RUN echo $0 > sh.txt
#   '/bin/sh' 以外の別のシェルを利用する場合
RUN /bin/bash -c 'echo $0 > bash.txt'

# exec 形式(コマンドシェルを起動せずに直接実行する)
RUN ["/bin/ls", "-l", "/"]
RUN ["/bin/bash", "-c", "echo $0 > exec.txt"]