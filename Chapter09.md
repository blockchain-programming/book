# Open Assets Protocol

## Marker Output

### Marker Outputの例

```ruby
>> marker = OpenAssets::Protocol::MarkerOutput.deserialize_payload("4f41010003ac0200e58e260412345678")

>> marker.asset_quantities
=> [300, 0, 624485]

>> marker.metadata
=> "\x124Vx"
```

## Asset IDの計算

```ruby
require 'bitcoin'

# 1.秘密鍵を生成
key = Bitcoin::Key.generate
private_key = key.priv
=> "e1b9722c716b568f2bb25d7fc9a4943b76369711fa33cc7a0b309307f8a4bfdd"

# 2.対応する公開鍵を生成
public_key = key.pub
=> "03ecf9321ba066d9b7e224803aae0df9104fed0553ab64acb5937c1b079cfb9da7"

# 3.P2PKHスクリプトを生成
script = Bitcoin::Script.to_hash160_script(key.hash160)

# 4.スクリプトのハッシュを生成
hash = Bitcoin.hash160(script.bth)
=> "f8cc4f23cdedeca2c429aef5b3345e666c411ece"

# 5. Asset IDとしてエンコード
hash = 23.to_s(16) + hash
asset_id = Bitcoin.encode_base58(hash + Bitcoin.checksum(hash))
=> "AeTPyiiyYKEFXWeu4WjDqjb4raPck2XWeJ"
```

## openassets-rubyを使ったアセットの発行・送付

### 設定

```ruby
require 'openassets'

Bitcoin.network = :testnet3

api = OpenAssets::Api.new({
    network:           'testnet',
    rpc: {
      user:                'xxx',
      password:            'xxx',
      schema:             'http',
      port:                 18332,
      host:          'localhost'}
  })
```

```ruby
utxo_list = api.list_unspent

puts JSON.pretty_generate(utxo_list)
=> [
     {
       "txid": "a98b000f28c9e8251925a1225832edf1c2992e103445b6b2cec3f5c0a81469e5",
       "vout": 2,
       "confirmations": 154856,
       "address": "mvZM34fU6wdDqF3gKd2tYA67vjWvwBHbDU",
       "oa_address": "bX6XEHEUoaRWuhVD3NF95C1zhGLh6qqqk5d",
       "script": "76a914a4fdb3ce954a3cbb49fdfc9df1712bf72749c9e788ac",
       "script_type": "pubkeyhash",
       "amount": "0.00039400",
       "asset_id": null,
       "asset_quantity": "0",
       "asset_amount": "0",
       "account": "sample",
       "asset_definition_url": "",
       "proof_of_authenticity": false,
       "output_type": "transfer",
       "solvable": true,
       "spendable": true
     },
...
   ]
```

### アセットの発行

```ruby
# ビットコインアドレス
btc_address = 'mxpc1LnTMDFxwy1Va6Xpd4p1nDwRUopeic'

# ビットコインアドレスからOpen Assets Address
oa_address = OpenAssets.address_to_oa_address(btc_address)

# メタデータ
metadata = 'u=https://goo.gl/uapCsJ'

# アセットの発行
tx = api.issue_asset(oa_address, 100, metadata)
=> 07e108133edcc3a5407de8e8f117a995625abcba2c8bb42ad0831c3665dbe29b # TXID
```

```ruby
# ビットコインアドレスから公開鍵のHash160を計算
hash160 = Bitcoin.hash160_from_address(btc_address)

# 公開鍵のHash160からAsset IDを計算
asset_id = OpenAssets.pubkey_hash_to_asset_id(hash160)
=> "oQDVsbKEAkkBz1nN5BTGUwMxu9Y5CmhFEU"

# 公開鍵が分かっていれば以下のメソッドで計算することも可能です
asset_id = OpenAssets.generate_asset_id(公開鍵)
```

### アセットの送付

```ruby
asset_id = 'oQDVsbKEAkkBz1nN5BTGUwMxu9Y5CmhFEU'

# 送付先のアドレス
to_oa_address = OpenAssets.address_to_oa_address('mvAQLozzMcAKPGyFwck4opDCQb86pmz3TZ')

# アセットを30送付
tx = api.send_asset(oa_address, asset_id, 30, to_oa_address)
=> 12712bdda9cbdd40268c0b95429a731ec3047692f45fe0599b4bd91739c0bd91 #TXID
```

```ruby
# 送付元アドレス
from = api.address_to_oa_address('mrxpeizRrF8ymNx5FrvcGGZVecZjtUFVP3')

# 送付先アドレス
to = api.address_to_oa_address('n4MEsSUN8GktDFZzU3V55mP3jWGMN7e4wE')

# 送付アセットと量
params = []
params << OpenAssets::SendAssetParam.new('oGu4VXx2TU97d9LmPP8PMCkHckkcPqC5RY', 50, to)
params << OpenAssets::SendAssetParam.new('oUygwarZqNGrjDvcZUpZdvEc7es6dcs1vs', 4, to)

tx = api.send_assets(from, params)
```

### ビットコインの送付

```ruby
# 送付元のビットコインアドレス
from = 'mt1hZLajqyc63NkWy7qvgiuum5nuTBdVZ6'

# 送付先のビットコインアドレス
to = 'mgfx7ZKuvutFozSc1aH1U1iDEgqM8bRDX2'

# ビットコインの送金
api.send_bitcoin(from, 2150000, to)
=> 11929789a686dbf6895b0d52bfe64ad1768b95c8458412cf9adadb9431403ca7 # TXID
```

### アセットの焼却

```ruby
oa_address = OpenAssets.address_to_oa_address('mxpc1LnTMDFxwy1Va6Xpd4p1nDwRUopeic')

asset_id = 'oQDVsbKEAkkBz1nN5BTGUwMxu9Y5CmhFEU'

# アセットの焼却
tx = api.burn_asset(oa_address, asset_id)
=> 4f67034b074edf5fa4880e5a30997d450ba5d1d59d0fb5b8dba6cf793f78e923 # TXID
```

### ユーティリティ

```ruby
# OP_RETURNのOpen Assets Payloadをパース
m = OpenAssets::Protocol::MarkerOutput.deserialize_payload('4f410100016417753d68747470733a2f2f676f6f2e676c2f75617043734a')

m.asset_quantities
=> [100] # Asset Quantity

m.metadata
=> "u=https://goo.gl/uapCsJ" # メタデータ

# Open Assets Payloadの生成
quantities = [500, 100, 15]
metadata = 'u=https://goo.gl/uapCsJ'

m = OpenAssets::Protocol::MarkerOutput.new(quantities, metadata)
payload = m.to_payload
=> "4f41010003f403640f17753d68747470733a2f2f676f6f2e676c2f75617043734a"

# Marker Outputスクリプトの生成
script = m.build_script
script.to_payload.bth
=> "6a214f41010003f403640f17753d68747470733a2f2f676f6f2e676c2f75617043734a"
```

```ruby
# Bitcoin Coreへアクセスするプロバイダの取得（OpenAssets::Provider::BitcoinCoreProvider）
provider = api.provider

# Bitcoin Coreの各種APIのよび出し

# getinfoの実行
provider.getinfo
=> {
     "version": 140100,
     "protocolversion": 70015,
     "walletversion": 60000,
     "balance": 0.0905132,
     "blocks": 1125666,
     "timeoffset": 0,
     "connections": 8,
     "proxy": "",
     "difficulty": 1,
     "testnet": true,
     "keypoololdest": 1458624560,
     "keypoolsize": 100,
     "paytxfee": 0.0,
     "relayfee": 1.0e-05,
     "errors": "Warning: unknown new rules activated (versionbit 28)"
   }

# getblockhashの実行
provider.getblockhash(1125666)
=> "000000000002945741d2fd9690fb9ebdecb2e11b3ab836666bb04c31a10a5713"

# getblockの実行
provider.getblock("000000000002945741d2fd9690fb9ebdecb2e11b3ab836666bb04c31a10a5713")
=> {
     "hash": "000000000002945741d2fd9690fb9ebdecb2e11b3ab836666bb04c31a10a5713",
     "confirmations": 1,
     "strippedsize": 36114,
     "size": 36114,
     "weight": 144456,
     "height": 1125666,
     "version": 536870912,
     "versionHex": "20000000",
     "merkleroot": "707ab240cc863361b7ce2429b30a21d4c771306947a28adf5e91e3fb1709c5ab",
     "tx": [
       "3e9831a06117c34f65a7575fb8bf2064ccce5ecd6081c596511ec5b5f84008aa",
       "d51d2a3d7cf4b54ad655d07bcc03f04a5bfa02117e90680d12fd3b8ca934cc2c",
       "508147ee0ec806fb1c322eafcab245bff871851b216e0565054cf88dbc2e9bf0",
       ...
       "190cd8167ed5e65e1b743e8ec4b8031ccb89d904d7584af34f295528d9eebfc6"
     ],
     "time": 1495762246,
     "mediantime": 1495756158,
     "nonce": 2559794018,
     "bits": "1d00ffff",
     "difficulty": 1,
     "chainwork": "00000000000000000000000000000000000000000000002457b3f28a59bccf62",
     "previousblockhash": "000000000000d6245eb09589e0efdabf5e76405220aeffdd12cc39d616b72282"
   }

# この他にもbitcoin-cliがサポートしているコマンドとその引数をそのまま実行できます。
```

