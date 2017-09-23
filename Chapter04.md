# ビットコインワレット

bitcoin core デーモンの起動

```
$ bitcoind &
```

bitcoin-cli コマンド

```
$ bitcoin-cli getblockcount

```

## ワレットの初期化

### 新しいアドレスの生成

```
$ bitcoin-cli getnewaddress
mr3xccBuBvKkLrsGx4y9XdhkkZQMHBoHKM
```

### アドレスにアカウントを設定する

```
$ bitcoin-cli setaccount msksQAKTiCFYC8MH7CqoKpH1FfvBGuAbeU SatoshiNakamoto
$ bitcoin-cli setaccount mjXM1dXRwowLjYPmyyWJEkLEvAH8CgSJmm SatoshiNakamoto
$ bitcoin-cli setaccount mr3xccBuBvKkLrsGx4y9XdhkkZQMHBoHKM MickeyMouse
```

アカウントごとの所持金残高を知る。

```
$ bitcoin-cli listaccounts
{
  "": 0.00000000,
  "MickeyMouse": 0.13000000,
  "NakamotoSatoshi": 1.30000000,
}
```

### ワレットの秘密鍵の暗号化

```
$ bitcoin-cli encryptwallet <パスワード>
```

```
$ bitcoin-cli walletpassphrase <パスワード> <保持時間（秒）>
```

```
$  bitcoin-cli walletlock
```

### testnetでのビットコインの入手


* [UO1 testnet Faucet :](http://bitcoinfaucet.uo1.net/)
* [TP's testnet Faucet :] (http://tpfaucet.appspot.com/)
* [flyingkiwi's testnet Faucet :] (https://testnet.manu.backend.hamburg/faucet)
* [nkuttler's Bitcoin testnet Faucet :] (https://kuttler.eu/en/bitcoin/btc/faucet/)


## ワレット残高を確認する

```
$ bitcoin-cli getbalance
```

### 自分宛のUTXOを確認する

```
$ bitcoin-cli listunspent
```

### bitcoin core のJSON RPC APIの利用


```ruby
require 'bitcoin'
require 'net/http'
require 'json'
Bitcoin.network = :testnet3
RPCUSER = <ユーザ名>　    	# JSON RPC のためのユーザ名
RPCPASSWORD = <パスワード>	# JSON RPC のためのパスワード
HOST="localhost"			# JSON RPC のhost
PORT=18332					# ポート番号
 
#bitcoindへのHTTPアクセスするメソッド
def bitcoinRPC(method,param)
    http = Net::HTTP.new(HOST, PORT)
    request = Net::HTTP::Post.new('/')
    request.basic_auth(RPCUSER,RPCPASSWORD)
    request.content_type = 'application/json'
    request.body = {method: method, params: param, id: 'jsonrpc'}.to_json
    JSON.parse(http.request(request).body)["result"]
end

```

```ruby
 bitcoinRPC('getbalance',[]) 	# ワレットの残高を得る
 bitcoinRPC('listunspent',[])	# UTXOのリストを得る
```

## ビットコインを送金する

### 取引手数料の既定値の設定

```
$ bitcoin-cli settxfee 0.00001
true
```

```
# 暗号化された秘密鍵を復号化するパスワードのセット
$ bitcoin-cli walletpassphrase <パスワード> <保持時間（秒）>
# ビットコインの送金の例
$ bitcoin-cli sendfrom MickeyMouse msksQAKTiCFYC8MH7CqoKpH1FfvBGuAbeU 0.1
45d3d4b716418fcf3a983df572d9faa9e799a7e36439cd415211e66554ecf381
# 秘密鍵の復号化に使用するパスワードをメモリから削除してワレットをロック状態に戻す
$ bitcoin-cli walletlock
```

```ruby
 bitcoinRPC('walletpassphrase',[<パスワード>, <保持時間（秒）>])
 bitcoinRPC('sendfrom',['MickeyMouse','msksQAKTiCFYC8MH7CqoKpH1FfvBGuAbeU',0.1])
 bitcoinRPC('walletlock',[])
```

### トランザクション内容の確認

`bitcoin-cli gettransaction <トランザクションID>`

実行例

```
$ bitcoin-cli gettransaction 45d3d4b716418fcf3a983df572d9faa9e799a7e36439cd415211e66554ecf381


```

`bitcoinRPC('gettransaction',['<トランザクションID>'])`

実行例

```ruby
 tx = bitcoinRPC('gettransaction',['45d3d4b716418fcf3a983df572d9faa9e799a7e36439cd415211e66554ecf381'])

```

```ruby
 tx.keys            # Hashとしてのトランザクションのキーの一覧
=> ["amount", "fee", "confirmations", "blockhash", "blockindex", "blocktime", "txid", "walletconflicts", "time", "timereceived", "bip125-replaceable", "details", "hex"]

 tx["details"]   	# トランザクションの詳細の例
=> [{"account"=>"MickeyMouse", "address"=>"msksQAKTiCFYC8MH7CqoKpH1FfvBGuAbeU", "category"=>"send", "amount"=>-0.1, "label"=>"SatoshiNakamoto", "vout"=>0, "fee"=>-2.31e-05, "abandoned"=>false}, {"account"=>"SatoshiNakamoto", "address"=>"msksQAKTiCFYC8MH7CqoKpH1FfvBGuAbeU", "category"=>"receive", "amount"=>0.1, "label"=>"SatoshiNakamoto", "vout"=>0}]

```

### トランザクションの一覧

```
$ bitcoin-cli listtransactions
```

Ruby版の実行例

```ruby
 tx_list = bitcoinRPC('listtransactions',[])
=> [{"account"=>"SatoshiNakamoto", "address"=>"msksQAKTiCFYC8MH7CqoKpH1FfvBGuAbeU", "category"=>"receive", "amount"=>0.1, "label"=>"SatoshiNakamoto", "vout"=>0, "confirmations"=>59, "blockhash"=>"0000000001ea44d8f4d84f43d75772d74ef170776f9f75ef221d44b24904707c", 
...
no", "abandoned"=>false}]

```

### ビットコインによる支払い請求の作成

```
 ビットコインURN     	   = "bitcoin:" ビットコインアドレス [ "?" bitcoinparams ]
 ビットコインアドレス 	= *base58
 bitcoinparams  		= amountparam [ "&" bitcoinparams ]
 bitcoinparam   		= [ 送金金額 / ラベル / メッセージ / 追加パラメータ / reqparam ]
 送金金額    			 = "amount=" *数値 [ "." *数値 ]
 ラベル     			 = "label=" *qchar
 メッセージ   			= "message=" *qchar
 追加パラメータ     	   = qchar *qchar [ "=" *qchar ]
 reqparam       		= "req-" qchar *qchar [ "=" *qchar ]
```


```ruby
 require 'uri'
 label = URI.encode("サトシ書店")
=> "%E3%82%B5%E3%83%88%E3%82%B7%E6%9B%B8%E5%BA%97"
 message = URI.encode("書籍代")
=> "%E6%9B%B8%E7%B1%8D%E4%BB%A3"
```

```
bitcoin:1kQiJ5E27JKPXzSpUVaWeXgnGXLRmfiVx?amount=0.02&label=%E3%82%B5%E3%83%88%E3%82%B7%E6%9B%B8%E5%BA%97&message=%E6%9B%B8%E7%B1%8D%E4%BB%A3
```

```
# rqrcode_pngライブラリのインストール
$ gem install rqrcode_png
```

```ruby
require 'rqrcode'
require 'rqrcode_png'

uri='bitcoin:1kQiJ5E27JKPXzSpUVaWeXgnGXLRmfiVx?amount=0.02&label=%E3%82%B5%E3%83%88%E3%82%B7%E6%9B%B8%E5%BA%97&message=%E6%9B%B8%E7%B1%8D%E4%BB%A3'

qr = RQRCode::QRCode.new(uri)         # QRコードの生成
png = qr.to_img                       # pngデータへの変換
png.resize(300, 300).save("bill.png") # サイズを300*300 に拡大して保存
```


## ビットコインワレットによる暗号鍵管理


### ルートシードの生成

```ruby
 require 'hdkey'
 require 'securerandom'
# ルートシードの生成
 root_seed=SecureRandom.hex(32)
=> "d4f5ebae05fd3ded311b55bc8fe9896467f890751a9eb6d48ad1e0d9fb1e19aa"
```

### マスター鍵の生成


```ruby
#　マスターキーの生成
 master_key = Bitcoin::ExtKey.generate_master(root_seed.htb)
# マスター秘密鍵
 m = master_key.priv
=> "92d0a269abb5700a086504279236c9ff671a337b05ea805a8d5d1da597bdce6f"
# マスター公開鍵
 M = master_key.pub
=> "023a051198a3f3209e70a0d84bf6858663dbbee1390a119967184fbf62a55903c9"
# マスターチェインコード
 master_key.chain_code
=> "\xC1\x81JO8k\xA6N\xEFLLf\xB9\xAA\x06\xAC\xA9\xF0\xBF?\xF0\xC4C\x96\xF8\xA1\xD3I\xBE\xAC\x87#"
#派生階層
 master_key.depth
=> 0
```
### 上位階層の鍵導出

#### 第2階層 m/44' の強化導出

```ruby
# 44' の鍵を強化導出
 key44 = master_key.derive(2**31+44)
# 鍵の派生階層
 key44.depth
=> 1
```

#### 第3階層 m/44'/0' の強化導出

```ruby
# インデックス0'強化導出するので、2**31+0
 key440=key44.derive(2**31+0)
# 鍵の派生階層
 key440.depth
=> 2
```

#### 第4階層のアカウントの強化導出

```ruby
# インデックス0'強化導出するので、2**31+0
 key4400=key440.derive(2**31+0)
# 鍵の派生階層
 key4400.depth
=> 3
# インデックス0'強化導出するので、2**31+1
 key4401=key440.derive(2**31+1)
# 鍵の派生階層
 key4401.depth
=> 3

# 拡張秘密鍵のシリアライズ
 key4400.to_base58
=> "xprv9z6uo3ZoHrWf6u5SvbZU4LGAmUHwrCT2mhXR46Xrr8naiimGUPM3g4dh78EMhhXMDfb3fA1Nav6SeJjtoaGouo1ktTHx1BYwidoAJx9eGgz"

# 拡張公開鍵のシリアライズ
 key4401.ext_pubkey.to_base58
=> "xpub6D6GCZ6h8E4xNAHKKBQdeJARQgXoyjtoYF3VfsDGoAERTbWBGBunTVnrLXRaWhR7kuVvh9qb1F4zp58P6Gqv5w9DkammPZCU93VWoKWY1ER"

# 拡張秘密鍵のインポート
ext_privkey = Bitcoin::ExtKey.from_base58("xprv9z6uo3ZoHrWf6u5SvbZU4LGAmUHwrCT2mhXR46Xrr8naiimGUPM3g4dh78EMhhXMDfb3fA1Nav6SeJjtoaGouo1ktTHx1BYwidoAJx9eGgz")

# 拡張公開鍵のインポート
xt_pubkey = Bitcoin::ExtPubkey.from_base58("xpub6D6GCZ6h8E4xNAHKKBQdeJARQgXoyjtoYF3VfsDGoAERTbWBGBunTVnrLXRaWhR7kuVvh9qb1F4zp58P6Gqv5w9DkammPZCU93VWoKWY1ER")
```

#### 第5階層、第6階層の導出

```ruby
# このアカウントのエクスターナルチェーン
 key44000 = key4400.derive(0)
# 鍵の派生階層
 key44000.depth
=> 4

# このアカウントのエクスターナルチェーンの外部公開用公開鍵
 key440000 = key44000.derive(0)
# 鍵の派生階層
 key440000.depth
=> 5
# このアカウントのエクスターナルチェーンの外部公開アドレス
 key440000.addr
=> "163fo3ebG6yo7bCTV7zJiyfWLbfZtaA9Ku"
```

