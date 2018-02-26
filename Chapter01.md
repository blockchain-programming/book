# ツールの導入

## Bitcoin Core

### Mac OS X の場合

#### ソースコードのクローン

```
$ git clone https://github.com/bitcoin/bitcoin.git
```

```
$ cd bitcoin
$ git tag
```

```
$ git checkout -b 0.16.0 refs/tags/v0.16.0
```

#### 依存パッケージのダウンロード

```
brew install automake berkeley-db4 libtool boost --c++11 miniupnpc openssl pkg-config homebrew/versions/protobuf260 --c++11 qt5 libevent
```

#### コンパイル

```
$ ./autogen.sh
$ ./configure
$ make -j4
$ make install
```

```
$ which bitcoind
/usr/local/bin/bitcoind

$ which bitcoin-cli
/usr/local/bin/bitcoin-cli
```

### Ubuntu の場合

#### パッケージファイルからインストール

```
$ sudo apt-add-repository ppa:bitcoin/bitcoin
$ sudo apt-get update
$ sudo apt-get install bitcoind bitcoin-qt
```

#### ソースコードからコンパイル

ソースコードのクローンとコンパイルの手順については Mac OS X と同様

##### 依存パッケージのダウンロード

```
$ sudo apt-get install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils
```

```
$ sudo apt-get install libboost-system-dev libboost-filesystem-dev libboost-chrono-dev libboost-program-options-dev libboost-test-dev libboost-thread-dev
または
$ sudo apt-get install libboost-all-dev
```

```
$ sudo add-apt-repository ppa:bitcoin/bitcoin
$ sudo apt-get update
$ sudo apt-get install libdb4.8-dev libdb4.8++-dev
```

```
$ sudo apt-get install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler
```

```
$ sudo apt-get install libqrencode-dev
```

```
$ sudo apt-get install libminiupnpc-dev
```

### 初期設定

#### Bitcoin Core でビットコイン・ネットワークに接続する

##### bitcoind の起動と停止

```
$ bitcoin-qt &
```

デーモンのみ

```
$ bitcoind -daemon
```

regtestモード

```
$ bitcoind -regtest -daemon
```

停止

```
$ bitcoin-cli stop
```

#### bitcoin-cli コマンドを使ってbitcoind と対話する

```
$ bitcoin-cli getblockchaininfo
```

##### RESTインターフェースを使ってbitcoind と対話する

```
$ curl localhost:18332/rest/chaininfo.json
```

##### ビットコイン・ネットワークのノードとの接続

```
$ bitcoin-cli getconnectioncount
```

```
$ bitcoin-cli getpeerinfo
```

##### regtestモードでのブロック生成

```
$ bitcoin-cli -regtest generate 3
```

## bitcoin-ruby

### インストール

```
$ sudo gem install bitcoin-ruby
```

Rubygemsがインストールされていない場合は、先にRubygemsをインストール
```
$ sudo apt install ruby
```


## openassets-ruby

### インストール

```
$ sudo gem install openassets-ruby
```

ruby.hが見つからない場合

```
$ sudo apt-get install ruby-dev
```

sqlite3が見つからない場合

```
$ sudo apt-get install libsqlite3-dev
```
