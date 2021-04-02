# References from Unix and Linux System Administration Handbook - fifth edition

- `man -k <keyword>` or `apropos <keyword>` prints a list of man pages that have *keyword* in their one line synopses

- determing whether software is already installed
    - `which` is relevant binary in your search path
    - `whereis` searches broader range and is independent of shell's search path
    - `locate` precompiled index of filesystem to locate filenames

- which file belongs to a package
    * ubuntu    `dpkg-query -S <path>`
    * redhat    `rpm -qf <paht>`
    * freeBSD   `pkg which <path>`


- installing packages (`tcpdump` as an example)
    * ubuntu    `sudo apt-get install tcpdump`
    * redhat    `sudo yum install tcpdump`
    * freeBSD   `sudo pkg install -y tcpdump`

- compiling package (common pattern)
```
cd /tmp
git clone <url to tcpdump repo>
cd tcpdump
./configure
make
sudo make install
```

or read `README.d` file or run `./configure --help`

## template
- template
    * ubuntu    `template`
    * redhat    `template`
    * freeBSD   `template`
