# トランザクション

## 標準的なトランザクションの構成方法

### トランザクションの具体例

```
$ bitcoin-cli decoderawtransaction 0100000001f11b0fdc283bba47b57a0eff0846848c5578b732909ddd5d2edd2d8537eabac0000000006a4730440220246ca676c6878cd55609751625904b1d14189909048f1957d5277d2ecd8fd726022034dad9a880e47c1f475df535b3ba1fa4867f8037ff9981bd5361c19436fa8e54012102321bc438a8f9d1aa69f1c730d9c84e0e85c2bddd0df4924ae851f082b205d646ffffffff01b01df505000000001976a914be7966685d48ebbc07c7104b16528455c3b157a888ac00000000
```

### 可変長整数
```
$ irb
>> require 'bitcoin'
>> Bitcoin::Protocol.pack_var_int(252)
>> Bitcoin::Protocol.pack_var_int(253)
>> Bitcoin::Protocol.pack_var_int(65535)
>> Bitcoin::Protocol.pack_var_int(65536)
```


## スクリプト言語

### スクリプトの作成

```
$ irb
>> require 'bitcoin'
>> Bitcoin::Script.from_string("2 4 OP_ADD 6 OP_EQUAL")	#文字列
>> Bitcoin::Script.new("RT\x93V\x87")					#16進数
>> Bitcoin::Script.new(["5254935687"].pack("H*"))		#バイナリ
```

### スクリプトの参照

```
$ irb
>> require 'bitcoin'
>> script = Bitcoin::Script.from_string("2 4 OP_ADD 6 OP_EQUAL")
>> script.to_string		#文字列
>> script.to_payload	#バイナリ
>> script.to_binary		#バイナリ
```

### スクリプトの検証

```
$ irb
>> require 'bitcoin'
>> Bitcoin::Script.from_string("2 4 OP_ADD 6 OP_EQUAL").run
>> Bitcoin::Script.from_string("2 3 OP_ADD 6 OP_EQUAL").run
```

```
$ irb
>> require 'bitcoin'
>> script = Bitcoin::Script.from_string("2 4 OP_ADD 6 OP_EQUAL")
>> script.run
>> script.debug
>> script.debug[0]
>> script.debug[1]
>> script.debug[2]
>> script.debug[3]
>> script.debug[4]
>> script.debug[5]
>> script.debug[6]
>> script.debug[7]
>> script.debug[8]
>> script.debug[9]
>> script.debug[10]
>> script.debug[11]
```


## トランザクションの作成

### Bitcoin CoreのAPIを使用する場合

#### インプットにセットするUTXOの準備

```
$ bitcoind -regtest -daemon
$ bitcoin-cli generate 101
```

```
$ bitcoin-cli listunspent
```

```
$ bitcoin-cli getrawtransaction 785149aedc5637c446d40674895f16f98c487be98972c5550a71bd929c4cbd2d 1
```

#### トランザクション全体の作成、インプットの作成、アウトプットの作成

```
$ bitcoin-cli getnewaddress
$ bitcoin-cli createrawtransaction "[{\"txid\":\"785149aedc5637c446d40674895f16f98c487be98972c5550a71bd929c4cbd2d\",\"vout\":0}]" "{\"mndTEiHWjkbQqeTrSNKB4YnvVY5XH1DZvv\":0.999}"
```

```
$ bitcoin-cli decoderawtransaction 01000000012dbd4c9c92bd710a55c57289e97b488cf9165f897406d446c43756dcae4951780000000000ffffffff01605af405000000001976a9144e038940f498b0880d259e7e8b2283f645087e8488ac00000000
```


### bitcoin-rubyを使用する場合

#### インプットにセットするUTXOの準備

```
$ irb
>> require 'bitcoin'
>> Bitcoin.network = :testnet3	#testnetの場合
>> Bitcoin.network = :regtest	#regtestの場合
```

#### トランザクション全体の作成

```
>> tx = Bitcoin::Protocol::Tx.new
```

#### インプットの作成

```
>> prev_tx_hash = "785149aedc5637c446d40674895f16f98c487be98972c5550a71bd929c4cbd2d"
>> prev_output_index = 0
>> tx_in = Bitcoin::Protocol::TxIn.from_hex_hash(prev_tx_hash, prev_output_index)
>> tx.add_in(tx_in)
```

#### アウトプットの作成

```
>> address = "mndTEiHWjkbQqeTrSNKB4YnvVY5XH1DZvv"
>> value = 99900000	
>> tx_out = Bitcoin::Protocol::TxOut.value_to_address(value, address)
>> tx.add_out(tx_out)
```

ScriptPubkeyを指定する場合
```
>> value = 99900000
>> script_pubkey = "v\xA9\x14N\x03\x89@\xF4\x98\xB0\x88\r%\x9E~\x8B\"\x83\xF6E\b~\x84\x88\xAC"
>> Bitcoin::Protocol::TxOut.new(value, pk_script)
```

#### トランザクションの確認

```
>> tx.to_payload.bth
```

## トランザクションへの電子署名

署名前のトランザクション
```
$ bitcoin-cli decoderawtransaction 01000000012dbd4c9c92bd710a55c57289e97b488cf9165f897406d446c43756dcae4951780000000000ffffffff01605af405000000001976a9144e038940f498b0880d259e7e8b2283f645087e8488ac00000000
```

### BitcoinCoreのAPIを使用する場合

```
$ bitcoin-cli signrawtransaction 01000000012dbd4c9c92bd710a55c57289e97b488cf9165f897406d446c43756dcae4951780000000000ffffffff01605af405000000001976a9144e038940f498b0880d259e7e8b2283f645087e8488ac00000000
```

### bitcoin-rubyを使用する場合

#### ALLで署名

```
$ bitcoin-cli dumpprivkey mxt67XLBNcnsFpWtFZCDq9e5poL9ecEwSX

$ irb
>> require 'bitcoin'
>> Bitcoin.network = :testnet3
>> tx = Bitcoin::Protocol::Tx.new("01000000012dbd4c9c92bd710a55c57289e97b488cf9165f897406d446c43756dcae4951780000000000ffffffff01605af405000000001976a9144e038940f498b0880d259e7e8b2283f645087e8488ac00000000".htb)	#署名前のトランザクション
>> prev_tx = Bitcoin::Protocol::Tx.new("0100000001f11b0fdc283bba47b57a0eff0846848c5578b732909ddd5d2edd2d8537eabac0000000006a4730440220246ca676c6878cd55609751625904b1d14189909048f1957d5277d2ecd8fd726022034dad9a880e47c1f475df535b3ba1fa4867f8037ff9981bd5361c19436fa8e54012102321bc438a8f9d1aa69f1c730d9c84e0e85c2bddd0df4924ae851f082b205d646ffffffff01b01df505000000001976a914be7966685d48ebbc07c7104b16528455c3b157a888ac00000000".htb)	#使用するUTXOが含まれるトランザクション
>> secret_key = "cTPqVpfbEPwPp8QG4cj4mMPWqA5UKpVVWWMvyK8WgsKjg5K5cyYu"
>> key = Bitcoin::Key.from_base58(secret_key)
>> sig_hash = tx.signature_hash_for_input(0, prev_tx, Bitcoin::Script::SIGHASH_TYPE[:all])	#ALLの範囲でsignature hashを作成
>> signature = key.sign(sig_hash)	#署名の実行
>> script_sig =  Bitcoin::Script.to_signature_pubkey_script(signature, key.pub.htb, Bitcoin::Script::SIGHASH_TYPE[:all])	#署名スクリプトの作成
>> tx.in[0].script_sig = script_sig	#署名スクリプトをインプットに追加
```

署名の検証

```
>> verify_tx =  Bitcoin::Protocol::Tx.new(tx.to_payload)	#トランザクションを複製
>> verify_tx.verify_input_signature(0, prev_tx)	#0番目のアウトプットについて署名を検証
```

ブロードキャスト

```
>> tx.to_payload.bth	#トランザクションの16進数を確認

$ bitcoin-cli sendrawtransaction 01000000012dbd4c9c92bd710a55c57289e97b488cf9165f897406d446c43756dcae495178000000006a473044022100f1f59a90054907dabcbf0f88cef63a54bfce89dcf715e2c972310c897faf09fd021f17fd17da54992684c2eaa9820286f56e0552e35df15423340b210a0ae21aa3012102321bc438a8f9d1aa69f1c730d9c84e0e85c2bddd0df4924ae851f082b205d646ffffffff01605af405000000001976a9144e038940f498b0880d259e7e8b2283f645087e8488ac00000000

$ bitcoin-cli getrawtransaction 4a9181bc83868084165b0bbafca7124e6c233f9ff26c587204bfda557fec2848 1	#ブロードキャストしたトランザクションの内容を確認
```

##### Secp256k1での署名

```
>> ENV['SECP256K1_LIB_PATH'] = '/usr/local/lib/libsecp256k1.so'		# Ubuntuの場合
>> ENV['SECP256K1_LIB_PATH'] = '/usr/local/lib/libsecp256k1.dylib'	# Mac OS X の場合

>> signature = Bitcoin::Secp256k1.sign(sig_hash, key.priv.htb) + [Bitcoin::Script::SIGHASH_TYPE[:all]].pack("C")
>> signature.bth
```

署名の検証

```
>> Bitcoin::Secp256k1.verify(sig_hash, signature, key.pub.htb)
```

鍵の生成

```
>> key = Bitcoin::Secp256k1.generate_key
>> key.addr	#ビットコインアドレス
>> key.pub	#公開鍵
>> key.priv	#秘密鍵
```


#### NONEで署名

```
$ irb
>> require 'bitcoin'
>> Bitcoin.network = :testnet3
>> tx = Bitcoin::Protocol::Tx.new("0100000001eaa0a91dd395453285a4a0430d9701fbce8b19f794ef53f603e5abdc66c5514f0000000000ffffffff01b0feea0b000000001976a9140e71550e6e5124cc0347a6622c5d8971a60a43d888ac00000000".htb)	#署名前のトランザクション
>> secret_key = "cS6oDaYpDSNwHZhG8H5xeuKVhrnwge17BQUjsrzkhzNaMPzzcrhZ"
>> key = Bitcoin::Key.from_base58(secret_key)

>> sig_hash = tx.signature_hash_for_input(0, prev_tx, Bitcoin::Script::SIGHASH_TYPE[:none])	#NONEの範囲でsignature hashを作成
>> signature = key.sign(sig_hash)	#署名を実行
>> script_sig = Bitcoin::Script.to_signature_pubkey_script(signature, key.pub.htb, Bitcoin::Script::SIGHASH_TYPE[:none])	＃署名スクリプトを作成
>> tx.in[0].script_sig = script_sig	#署名スクリプトをインプットに追加
>> tx.to_payload.bth
```

新しいアウトプットで置き換え
```
>> steal_tx =  Bitcoin::Protocol::Tx.new( "0100000001eaa0a91dd395453285a4a0430d9701fbce8b19f794ef53f603e5abdc66c5514f000000006a473044022074e36984a6788b705e7f504210b23587542d80c2bf481115f8425e4bfd2f365302205410d3dcd848cc28607538acb61447c0761759c1e102bd238802b6d5762bed840221032db652232e705c3d38f08ef484524e9da7899de36955a2f52685960a51c12303ffffffff01b0feea0b000000001976a9140e71550e6e5124cc0347a6622c5d8971a60a43d888ac00000000".htb)	#トランザクションを複製
>> steal_address = "n4LfUUnpNnMpLT6PiE8yPbFztiZF7CxDpK"	#新しいアドレス
>> value = 199950000
>> steal_script_pubkey = Bitcoin::Script.new(Bitcoin::Script.to_address_script(steal_address)).to_payload
>> steal_tx_out = Bitcoin::Protocol::TxOut.new(value, steal_script_pubkey)	#新しいアドレス宛のアウトプットを作成
>> steal_tx.out[0] = steal_tx_out	#新しいアウトプットを設定
>> verify_steal_tx =  Bitcoin::Protocol::Tx.new(steal_tx.to_payload)
>> verify_steal_tx.verify_input_signature(0, prev_tx)	#アウトプットを入れ替えたトランザクションで署名の検証を実施
```

アウトプットを入れ替えたトランザクションをブロードキャスト
```
>> steal_tx.to_payload.bth

$ bitcoin-cli sendrawtransaction 0100000001eaa0a91dd395453285a4a0430d9701fbce8b19f794ef53f603e5abdc66c5514f000000006a473044022074e36984a6788b705e7f504210b23587542d80c2bf481115f8425e4bfd2f365302205410d3dcd848cc28607538acb61447c0761759c1e102bd238802b6d5762bed840221032db652232e705c3d38f08ef484524e9da7899de36955a2f52685960a51c12303ffffffff01b0feea0b000000001976a914fa58b14cc42b4409989a7f2a230dfb15bdbd9b5a88ac00000000

$ bitcoin-cli getrawtransaction 39b4f322d1bfdcd7b8789c6921619e074be521c2f2b5a1dc51abe481191d9bd4 1
```

## P2PKH、マルチシグ、P2SH

## P2PKH (Pay to Public Key Hash)

### ScriptPubkeyの実装例

```
$ irb
>> require 'bitcoin'
>> Bitcoin.network = :testnet3
>> address = "n1PEN5QVu3HcZ2Tft2MSgGqLGhws7JB9EJ"
>> script_pubkey = Bitcoin::Script.to_address_script(address)
>> Bitcoin::Script.new(script_pubkey).to_string	#ScriptPubkeyの中身を確認
```

### ScriptSigの実装例

```
>> sig_hash = tx.signature_hash_for_input(0, prev_tx, Bitcoin::Script::SIGHASH_TYPE[:all])
>> signature = key.sign(sig_hash)
>> script_sig =  Bitcoin::Script.to_signature_pubkey_script(signature, key.pub.htb, Bitcoin::Script::SIGHASH_TYPE[:all])
>> Bitcoin::Script.new(script_sig).to_string	#ScriptSigの中身を確認
```

## マルチシグ

### ScriptPubkeyの実装例
```
$ irb
>> require 'bitcoin'
>> public_key_a = "0357cf4ce57e92a0c15a989b351c31e69db9c75dcc5932e9a6d5fc0fc64947422b"
>> public_key_b = "036671fad937127aaf45226bcb9e6e94e289eda721d1d9e94992feb78af78e83e5"
>> public_key_c = "028f1ff70b9b502862133e3d7f0597492776007287c092f3ee1fff13051dbb3857"
>> script_pubkey = Bitcoin::Script.to_multisig_script(2, public_key_a, public_key_b, public_key_c)
>> Bitcoin::Script.new(script_pubkey).to_string	#ScriptPubkeyの中身を確認
```

### ScriptSigの実装例

```
>> sig_hash = tx.signature_hash_for_input(0, prev_tx, Bitcoin::Script::SIGHASH_TYPE[:all])
>> signature_a = key_a.sign(sig_hash)
>> signature_b = key_b.sign(sig_hash)
>> script_sig_b = Bitcoin::Script.to_multisig_script_sig(signature_b)	#Bが署名
>> script_sig_ab = Bitcoin::Script.add_sig_to_multisig_script_sig(signature_a, script_sig_b)	#Aの署名を追加
>> Bitcoin::Script.new(script_sig).to_string	#ScriptSigの中身を確認
```

## P2SH (Pay to Script Hash)

### ScriptPubkeyの実装例

```
$ irb
>> require 'bitcoin'
>> public_key_a = "0357cf4ce57e92a0c15a989b351c31e69db9c75dcc5932e9a6d5fc0fc64947422b"
>> public_key_b = "036671fad937127aaf45226bcb9e6e94e289eda721d1d9e94992feb78af78e83e5"
>> public_key_c = "028f1ff70b9b502862133e3d7f0597492776007287c092f3ee1fff13051dbb3857"
>> script_pubkey, redeem_script = Bitcoin::Script.to_p2sh_multisig_script(2, public_key_a, public_key_b, public_key_c)
>> Bitcoin::Script.new(script_pubkey).to_string	#ScriptPubkeyの中身を確認
```

### ScriptSigの実装例

```
>> redeem_script = "R!\x03W\xCFL\xE5~\x92\xA0\xC1Z\x98\x9B5\x1C1\xE6\x9D\xB9\xC7]\xCCY2\xE9\xA6\xD5\xFC\x0F\xC6IGB+!\x03fq\xFA\xD97\x12z\xAFE\"k\xCB\x9En\x94\xE2\x89\xED\xA7!\xD1\xD9\xE9I\x92\xFE\xB7\x8A\xF7\x8E\x83\xE5!\x02\x8F\x1F\xF7\v\x9BP(b\x13>=\x7F\x05\x97I'v\x00r\x87\xC0\x92\xF3\xEE\x1F\xFF\x13\x05\x1D\xBB8WS\xAE"
>> sig_hash = tx.signature_hash_for_input(0, redeem_script)
>> signature_a = key_a.sign(sig_hash)
>> signature_b = key_b.sign(sig_hash)
>> script_sig = Bitcoin::Script.to_p2sh_multisig_script_sig(redeem_script, signature_a, signature_b)
>> Bitcoin::Script.new(script_sig).to_string	#ScriptSigの中身を確認
```

### 署名の順番をソートする実装例

```
>> sig_hash = tx.signature_hash_for_input(0, redeem_script)
>> signature_a = key_a.sign(sig_hash)
>> signature_b = key_b.sign(sig_hash)
>> script_sig = Bitcoin::Script.to_p2sh_multisig_script_sig(redeem_script, signature_b, signature_a)
>> Bitcoin::Script.new(script_sig).to_string	# OP_0 <Signature B> <Signature A> <redeem script> の形
>> new_script_sig = Bitcoin::Script.sort_p2sh_multisig_signatures(script_sig, sig_hash)	#署名の順番をソート
>> Bitcoin::Script.new(new_script_sig).to_string	# OP_0 <Signature A> <Signature B> <redeem script> の形
```

## ロックタイム(OP_CLTV、OP_CSV)

### OP_CLTV(OP_CHECKLOCKTIMEVERIFY)

#### ScriptPubkeyの実装例

```
$ bitcoin-cli getblockcount
>> public_key = "0357cf4ce57e92a0c15a989b351c31e69db9c75dcc5932e9a6d5fc0fc64947422b"
>> expiry_time = 1089535
>> et = expiry_time.to_bn.to_s(2).reverse.unpack("H*")[0]
>> script_pubkey = Bitcoin::Script.from_string("#{et} OP_NOP2 OP_DROP #{public_key} OP_CHECKSIG").to_payload
>> Bitcoin::Script.new(script_pubkey).to_string	#ScriptPubkeyの中身を確認
```

#### ScriptSigの実装例

```
>> tx.lock_time = expiry_time
>> tx.in[0].sequence = [0].pack("V")	#sequenceに値を設定
>> sig_hash = tx.signature_hash_for_input(0, prev_tx, Bitcoin::Script::SIGHASH_TYPE[:all])
>> signature = key_a.sign(sig_hash)
>> script_sig = Bitcoin::Script.pack_pushdata(signature + [hash_type].pack("C"))
```

### OP_CSV(OP_CHECKSEQUENCEVERIFY)

#### ScriptPubkeyの実装例

```
>> public_key = "0357cf4ce57e92a0c15a989b351c31e69db9c75dcc5932e9a6d5fc0fc64947422b"
>> expiry_time = 6
>> script_pubkey = Bitcoin::Script.from_string("#{expiry_time} OP_NOP3 OP_DROP #{public_key} OP_CHECKSIG").to_payload
>> Bitcoin::Script.new(script_pubkey).to_string	#ScriptPubkeyの中身を確認
```

ブロック数が16を超える場合
```
>> expiry_time = 100
>> et = expiry_time.to_bn.to_s(2).reverse.unpack("H*")[0]
>> script_pubkey = Bitcoin::Script.from_string("#{et} OP_NOP3 OP_DROP #{public_key} OP_CHECKSIG").to_payload
>> Bitcoin::Script.new(script_pubkey).to_string	#ScriptPubkeyの中身を確認
```

#### ScriptSigの実装例

```
>> tx.ver = 2
>> tx.in[0].sequence = [expiry_time].pack("V")
>> sig_hash = tx.signature_hash_for_input(0, prev_tx, Bitcoin::Script::SIGHASH_TYPE[:all])
>> signature = key_a.sign(sig_hash)
>> script_sig = Bitcoin::Script.pack_pushdata(signature + [hash_type].pack("C"))
>> Bitcoin::Script.new(script_sig).to_string	#ScriptSigの中身を確認
```
