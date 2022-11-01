```bash
apk add --update git alpine-sdk build-bas libtool automake m4 autoconf linux-headers unzip ncurses ncurses-dev ncurses-libs ncurses-terminfo python python-dev py-pip clang go nodejs xz curl make cmake && rm -rf /var/cache/apk/*
```

```bash
git clone https://github.com/neovim/libtermkey.git --depth 1 && cd libtermkey && make && make install && cd ../ && rm -rf libtermkey
```

```bash
git clone https://github.com/neovim/libvterm.git --depth 1 && cd libvterm && make && make install && cd ../ && rm -rf libvterm
```

```bash
git clone https://github.com/neovim/unibilium.git --depth 1 && cd unibilium && autoreconf -fi && ./configure && make && make install && cd ../ && rm -rf unibilium
```

```bash
git clone https://github.com/neovim/neovim.git --depth 1 && cd neovim && make && make install && cd ../
```

