---
tags:
    - Error
    - Python
---
# Fix pip install gcc error

## Environment

- python 3.6
- pip 9.0.3
- almalinux 8.7

## Logs
```
$ pip3 install RPi.GPIO
Collecting RPi.GPIO
  Using cached RPi.GPIO-0.7.1.tar.gz (29 kB)
Using legacy 'setup.py install' for RPi.GPIO, since package 'wheel' is not installed.
Installing collected packages: RPi.GPIO
    Running setup.py install for RPi.GPIO ... error
    ERROR: Command errored out with exit status 1:
     command: /usr/bin/python3.9 -u -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-ie69gdzv/rpi-gpio/setup.py'"'"'; __file__='"'"'/tmp/pip-install-ie69gdzv/rpi-gpio/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' install --record /tmp/pip-record-hfhlbbcx/install-record.txt --single-version-externally-managed --compile --install-headers /usr/local/include/python3.9/RPi.GPIO
         cwd: /tmp/pip-install-ie69gdzv/rpi-gpio/
    Complete output (15 lines):
    running install
    running build
    running build_py
    creating build
    creating build/lib.linux-aarch64-3.9
    creating build/lib.linux-aarch64-3.9/RPi
    copying RPi/__init__.py -> build/lib.linux-aarch64-3.9/RPi
    creating build/lib.linux-aarch64-3.9/RPi/GPIO
    copying RPi/GPIO/__init__.py -> build/lib.linux-aarch64-3.9/RPi/GPIO
    running build_ext
    building 'RPi._GPIO' extension
    creating build/temp.linux-aarch64-3.9
    creating build/temp.linux-aarch64-3.9/source
    gcc -pthread -Wno-unused-result -Wsign-compare -DDYNAMIC_ANNOTATIONS_ENABLED=1 -DNDEBUG -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -fasynchronous-unwind-tables -fstack-clash-protection -D_GNU_SOURCE -fPIC -fwrapv -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -fasynchronous-unwind-tables -fstack-clash-protection -D_GNU_SOURCE -fPIC -fwrapv -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -fasynchronous-unwind-tables -fstack-clash-protection -D_GNU_SOURCE -fPIC -fwrapv -fPIC -I/usr/include/python3.9 -c source/c_gpio.c -o build/temp.linux-aarch64-3.9/source/c_gpio.o
    error: command 'gcc' failed: No such file or directory
    ----------------------------------------
ERROR: Command errored out with exit status 1: /usr/bin/python3.9 -u -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-ie69gdzv/rpi-gpio/setup.py'"'"'; __file__='"'"'/tmp/pip-install-ie69gdzv/rpi-gpio/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' install --record /tmp/pip-record-hfhlbbcx/install-record.txt --single-version-externally-managed --compile --install-headers /usr/local/include/python3.9/RPi.GPIO Check the logs for full command output.
```

## Instructions

- #### install dependencies
```
$ dnf install python3-libs python3-tools python3-devel gcc
```

- #### run pip install
```
$ pip3 install RPi.GPIO
collecting RPi.GPIO
  Using cached https://files.pythonhosted.org/packages/c4/0f/10b524a12b3445af1c607c27b2f5ed122ef55756e29942900e5c950735f2/RPi.GPIO-0.7.1.tar.gz
Installing collected packages: RPi.GPIO
  Running setup.py install for RPi.GPIO ... done
Successfully installed RPi.GPIO-0.7.1
```