してみたいよね

Linux
せっかくなら最新版を使いたいので、aptではなく下のようにインストールします。[公式手順](https://github.com/neovim/neovim/blob/master/INSTALL.md)に則っています
```bash
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.tar.gz
sudo rm -rf /opt/nvim-linux-x86_64
sudo tar -C /opt -xzf nvim-linux-x86_64.tar.gz

export PATH="$PATH:/opt/nvim-linux-x86_64/bin"
```
終わった後
```bash
nvim
```
で起動できるはずです

起動後、
```
:Tutor
```
でチュートリアルが出来ます
