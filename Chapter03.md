# 3章ビットコインの暗号技術

この章は、シェルからのコマンド入力とirbによる実行が中心なので、カットアンドペーストで実行していってみましょう。

## 利用する暗号ライブラリの準備
privKey
以下のようにして rubygemsのライブラリをインストールします。

シェルから

```
$ gem install ecdsa
$ gem install bitcoin
```

### openssl

openssl ライブラリはインストール不要です。

以下、irbのプロンプト >> は省力します。

```ruby
$ irb

 # 以下 irbのプロンプト >> から入力
 
 require 'openssl'
 ecdsa = OpenSSL::PKey::EC.new('secp256k1') 
 key = ecdsa.generate_key                        # 新しい暗号鍵を生成
 key.private_key.to_i                            # 秘密鍵を整数として取り出す
```

### securerandom による安全な乱数

```ruby
 require 'securerandom'
 SecureRandom.hex(32)     # 32バイト(=256ビット)の疑似乱数を生成する
```

### ruby_ecdsa

```ruby
 require 'ecdsa'
 group = ECDSA::Group::Secp256k1
```

### bitcoin-ruby

```ruby
 require 'bitcoin'
 Bitcoin.network = :testnet3
 key = Bitcoin::generate_key                   # 暗号鍵の生成
```

## ビットコインに利用されている暗号技術の基本

### 暗号学的ハッシュ関数

```ruby
 require 'digest'
 message = "暗号学的ハッシュ関数とは、直感的には任意の長さの文字列を入力すると一定のサイズのランダムな数値を返すような関数です。"
 Digest::SHA256.digest(message)                          # sha256のハッシュ値を求める
 Digest::RMD160.digest(message)                          # ripemd160のハッシュ値を求める

 require 'securerandom'
 nonce = SecureRandom.hex(32)                            # コミットメントのためのnonce
 commitment = Digest::SHA256.digest(nonce + message)     # sha256のハッシュ値を求める
```

## ECDSA

```ruby
require 'openssl'
curves = OpenSSL::PKey::EC.builtin_curves

 group = ECDSA::Group::Secp256k1            # secp256k1のパラメータセットのオブジェクト生成
 group.field.prime                          # 剰余系の素数p
=> 115792089237316195423570985008687907853269984665640564039457584007908834671663
 group.generator.x                          # 基準点Gのx座標
=> 55066263022277343669578718895168534326250603453777594175500187360389116729240
 group.generator.y                          # 基準点Gのy座標
=> 32670510020758816978083085130507043184471273380659243275938904335757337482424
 group.param_a                              # 係数a
=> 0
 group.param_b                              # 係数b
=> 7
 group.order                                # Gの位数
=> 115792089237316195423570985008687907852837564279074904382605163141518161494337
```

### ECDSAの暗号鍵の生成
privKey
```ruby
 n = group.order                                      # Gの位数
 privKey = 1 + SecureRandom.random_number(n-1)         # 秘密鍵の生成
 pubKey = group.generator.multiply_by_scalar(privKey)  # 公開鍵の生成
 pubKey.x                                             # 公開鍵のx座標の値
 pubKey.y                                             # 公開鍵のy座標の値
```

bitcoin-rubyの場合

```ruby
 require 'bitcoin'
 key = Bitcoin::generate_key    # 秘密鍵と公開鍵の配列を出力する
 privKey=key[0]                  # 秘密鍵
 pubKey=key[1]                  # 公開鍵 
```

ruby-opensslの場合

```ruby
 require 'openssl'
 ec = OpenSSL::PKey::EC.new('secp256k1')       # secp256k1楕円曲線暗号オブジェクトの生成
 key=ec.generate_key                           # 秘密鍵と公開鍵を生成
 privKey = key.private_key.to_i                 # 秘密鍵を整数で表示
 key.public_key.to_bn.to_i                     # 公開鍵を整数で表示
```

### ECDSAによる電子署名

```ruby
 require 'digest'
 message = "この文字列が電子署名の対象となるメッセージです"
 digest = Digest::SHA256.hexdigest(message)          # メッセージのダイジェスト
 privKey                                              # 署名者の秘密鍵
 pubKey                                              # 署名者の公開鍵
 k = 1 + SecureRandom.random_number(n-1)             # ランダムな整数kを選択
 sign = ECDSA.sign(group,privKey,digest,k)            # 電子署名の作成

 sign.r                                              # 電子署名の rの確認
 sign.s                                              # 電子署名の sの確認

```

#### 電子署名のバイナリエンコーディング

```ruby
 der_sign = ECDSA::Format::SignatureDerString.encode(sign)
```

#### 電子署名の検証

```ruby
 digest = OpenSSL::Digest::SHA256.hexdigest(message)        # メッセージのダイジェスト
 sign = ECDSA::Format::SignatureDerString.decode(der_sign)  # DER形式の電子署名をデコード
 
 ECDSA.valid_signature?(pubKey, digest, sign)               # 電子署名の検証
```

ruby-opensslの場合

```ruby
require 'openssl'
 ec = OpenSSL::PKey::EC.new('secp256k1') 
 
 key=ec.generate_key                                  # 暗号鍵ペアの生成
 message = "この文字列が電子署名の対象となるメッセージです"
 digest = OpenSSL::Digest::SHA256.hexdigest(message)  # メッセージのダイジェスト
 sign = key.dsa_sign_asn1(digest)                     # 電子署名の作成

 key.dsa_verify_asn1(digest,sign)                     # 電子署名の検証

```

### ビットコインアドレス

#### base58エンコーディング

```ruby
# base58エンコーディングを行うRuby プログラム

A = "123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz"
remain = ->n{(n<58 ? A[n] : [remain[n/58],A[n%58]])}
base58encode = ->h{remain[h.hex].flatten.join}

#　実行例
 base58encode["6f98c2e2dba3c5befb3892f0b09010abd920ae1a75bcc96fe3"]
=> "muSgU96EZbyokvEwhZPQvmRJPEB7B97CUA"
```

#### base58デコーディング

```ruby
# base58デコーディングを行うRuby プログラム

A = "123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz"
base58decode = ->b{b.reverse.split('').map.with_index{|c,i| A.index(c)*58**i}.reduce(:+).to_s(16)}

#　実行例
 base58decode["muSgU96EZbyokvEwhZPQvmRJPEB7B97CUA"]
=> "6f98c2e2dba3c5befb3892f0b09010abd920ae1a75bcc96fe3"
```

#### ペイロード

```ruby
 require 'bitcoin'
 key = Bitcoin::generate_key                       # 暗号鍵ペアの生成  
 pubKey = [key[1]].pack("H*")                      # 16進数文字列表現を1文字ずつ4ビットずつのビット列にする

 include Digest                                    # Digestクラスの名前空間を登録
 payload = RMD160.hexdigest(SHA256.digest(pubKey)) # ペイロードを作成

 payload6f = ['6f'+ payload].pack("H*")            # testnetのチェックサム用ペイロード

 checksum = SHA256.hexdigest(SHA256.digest(payload6f) )[0...8] # testnetのチェックサム

 address = Bitcoin::encode_base58('6f'+ payload +checksum)     # testnetのアドレス

 payload00 = ['00'+ payload].pack("H*")            # mainnetのチェックサム用ペイロード

 checksum = SHA256.hexdigest(SHA256.digest(payload00) )[0...8] # mainnetのチェックサム

 address = Bitcoin::encode_base58('00'+ payload + checksum)    # mainnetのアドレス

```

bitcoin-rubyの場合

```ruby
 Bitcoin::network = :testnet3                      # testnet の場合
 address = Bitcoin::pubkey_to_address(key[1])     # testnetアドレスの生成

 Bitcoin::network = :bitcoin                      # mainnet の場合

 address = Bitcoin::pubkey_to_address(key[1])     # mainnetアドレスの生成
```

#### Base58Checkエンコーディング

```ruby
 require 'bitcoin'
 base58data = "muSgU96EZbyokvEwhZPQvmRJPEB7B97CUA"     # Base58Check形式のデータ

 Bitcoin.base58_checksum?(base58data)                  # Base58Check形式の検査

```

## 暗号鍵のフォーマット

### 公開鍵のフォーマット

#### 非圧縮形式の公開鍵

```ruby
 x = "8b7a293ef55f76be5bbcd72de1d767e568f218f7713c000e3abc15662e3af6cc"
 y = "bf01df16760f317b796ecb799701fcbc776a781eaa6806d88b31e1e187d45a5f"
 pubKey = "04" + x + y

 pubKey   # 公開鍵
```

#### 圧縮形式の公開鍵

```ruby
 x = "8b7a293ef55f76be5bbcd72de1d767e568f218f7713c000e3abc15662e3af6cc"
 y = "bf01df16760f317b796ecb799701fcbc776a781eaa6806d88b31e1e187d45a5f" # yは奇数
 compressed_pubKey = "03" + x      # y が奇数の場合、プレフィックス "03"をつける


 x = "b724a33a5ff35d56a66ab8cf3a314252d8680c35d2e6cc385747d03c8860919c"
 y = "6967c2222dc07b674e6207c81972fd162d12f76c7a4ac603cf057fa34d6b09e2" # yは偶数
 compressed_pubKey = "02" + x      # y が偶数の場合、プレフィックス "02"をつける
=> "02b724a33a5ff35d56a66ab8cf3a314252d8680c35d2e6cc385747d03c8860919c" 


# 非圧縮形式から圧縮形式に変換するRubyプログラム
to_compressed_pubKey = -> pk{(pk.to_i(16).odd? ? "03"+pk[2..65] : "02"+pk[2..65])}

# 実行例
 pubKey1 = "048b7a293ef55f76be5bbcd72de1d767e568f218f7713c000e3abc15662e3af6ccbf01df16760f317b796ecb799701fcbc776a781eaa6806d88b31e1e187d45a5f"  # yが奇数
 pubKey2 = "04b724a33a5ff35d56a66ab8cf3a314252d8680c35d2e6cc385747d03c8860919c6967c2222dc07b674e6207c81972fd162d12f76c7a4ac603cf057fa34d6b09e2"  # yが偶数
 to_compressed_pubKey[pubKey1]
=> "038b7a293ef55f76be5bbcd72de1d767e568f218f7713c000e3abc15662e3af6cc"
 to_compressed_pubKey[pubKey2]
=> "02b724a33a5ff35d56a66ab8cf3a314252d8680c35d2e6cc385747d03c8860919c"
```


