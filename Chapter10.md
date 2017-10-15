# Segregated Witness

## Segwitのアドレスフォーマット

### Bech32フォーマット

#### チェックサム

```ruby
def create_checksum(hrp, data)
  values = expand_hrp(hrp) + data
  polymod = polymod(values + [0, 0, 0, 0, 0, 0]) ^ 1
  (0..5).map{|i|(polymod >> 5 * (5 - i)) & 31}
end

def expand_hrp(hrp)
  hrp.each_char.map{|c|c.ord >> 5} + [0] + hrp.each_char.map{|c|c.ord & 31}
end

def polymod(values)
  generator = [0x3b6a57b2, 0x26508e6d, 0x1ea119fa, 0x3d4233dd, 0x2a1462b3]
  chk = 1
  values.each do |v|
    top = chk >> 25
    chk = (chk & 0x1ffffff) << 5 ^ v
    (0..4).each{|i|chk ^= ((top >> i) & 1) == 0 ? 0 : generator[i]}
  end
  chk
end
```

```ruby
> create_checksum('A', [1, 2, 3,4])
=> [14, 5, 25, 5, 22, 26]
```

また、human-readable部（hrp）とデータ部（data）が与えられた際のチェックサムの有効性は以下のメソッドで検証できます。

```ruby
def verify_checksum(hrp, data)
  polymod(expand_hrp(hrp) + data) == 1
end
```

```ruby
> verify_checksum('A', [1, 2, 3,4, 14, 5, 25, 5, 22, 26])
=> true
```

### Segwitアドレス

```ruby
require 'bech32'
require 'digest'

pubkey = '0279BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798'

# 公開鍵をHASH160
hash160 = Digest::RMD160.hexdigest Digest::SHA256.digest([pubkey].pack("H*"))

# P2WPKHのscriptPubkeyを生成
script_pubkey = [["00", "14", hash160].join ].pack("H*").unpack("H*").first
=> "0014751e76e8199196d454941c45d1b3a323f1433bd6"

segwit_addr = Bech32::SegwitAddr.new
segwit_addr.hrp = 'bc'
segwit_addr.script_pubkey = script_pubkey

segwit_addr.addr
=> "bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4"

# Testnetの場合のアドレス
segwit_addr.hrp = 'tb'
segwit_addr.addr
=> "tb1qw508d6qejxtdg4y5r3zarvary0c5xw7kxpjzsx"
```

```ruby
segwit_addr = Bech32::SegwitAddr.new('bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kv8f3t4')

segwit_addr.to_script_pubkey
=> "0014751e76e8199196d454941c45d1b3a323f1433bd6"

segwit_addr.hrp
=> "bc"
```

## Segwitトランザクションの新しい署名方式

### P2WPKHの署名の作成

```ruby
require 'bitcoin'
Bitcoin.network = :testnet3

pubkey = '02effb2edfcf826d43027feae226143bdac058ad2e87b7cec26f97af2d357ddefa'
script_pubkey = Bitcoin::Script.to_witness_hash160_script(Bitcoin.hash160(pubkey)).bth
=> '00148911455a265235b2d356a1324af000d4dae03262'
```

```ruby
require 'bitcoin'
Bitcoin.network = :testnet3

# 公開鍵に対応する秘密鍵
key = Bitcoin::Key.from_base58('cT1HdGwS1vFwZsXqVzAKx1G8JYuwmM2sXtohU43uwXdNs6F7Xr2J')

# 上記P2WPKHのScriptPubkey宛の送金アウトプット（0番目のアウトプット）を含むトランザクション
prev_tx_payload = '0100000001f55cb86d8d04d4759fb8b05a198cf4d48d790e6c64d00e072aed98281d0ebff1010000006b483045022100fe718c5f0bb58d86225e1d9370f858b8c864f00112c6a33f9910fa7ea8e9c34b02203cef1a73a0a8e210c6a264446bbb90c86b7b6f6e6f411aa505f0a835ff147204012102effb2edfcf826d43027feae226143bdac058ad2e87b7cec26f97af2d357ddefaffffffff02806d0d00000000001600148911455a265235b2d356a1324af000d4dae0326250a1c917000000001976a9142c159d64daa0de5ae6abac61a9416c8a54e834bd88ac00000000'.htb
prev_tx = Bitcoin::Protocol::Tx.new(prev_tx_payload)

# P2WPKHのScriptPubkeyを使用するトランザクションを作成
tx = Bitcoin::Protocol::Tx.new
tx.add_in(Bitcoin::Protocol::TxIn.from_hex_hash(prev_tx.hash, 0))
tx.add_out(Bitcoin::Protocol::TxOut.value_to_address(870000, 'mt1hZLajqyc63NkWy7qvgiuum5nuTBdVZ6'))

# Segwit用の新しいフォーマットで署名対象のダイジェストを生成(ロックされているコインの量も必要になります。)
sig_hash = tx.signature_hash_for_witness_input(0, prev_tx.out[0].script, prev_tx.out[0].value)

# 秘密鍵で署名
sig = key.sign(sig_hash) + [Bitcoin::Script::SIGHASH_TYPE[:all]].pack("C")

# 生成した署名値と公開鍵をwitness領域にセット(script_sigにはセットしない)
tx.in[0].script_witness.stack << sig
tx.in[0].script_witness.stack << key.pub.htb

# 生成した署名が有効か検証(ロックされているコインの量も必要になります。)
tx.verify_witness_input_signature(0, prev_tx.out[0].script, prev_tx.out[0].value)
=> true

# Segwitのシリアライゼーションフォーマットのペイロードを生成
tx.to_witness_payload.bth
=> "010000000001018015516590902931d31f650f7e0e79a931e01bcb2f73d4ca49195aed2854b5fd0000000000ffffffff0170460d00000000001976a9148911455a265235b2d356a1324af000d4dae0326288ac0247304402207999b9ad187981fdda5606d0b4aa36fbbf5afe2ca234b5819420756cc103274802203eabb26cc5d0f5510380735283934935b70beb1a8836bbaaee9244c2fda0c830012102effb2edfcf826d43027feae226143bdac058ad2e87b7cec26f97af2d357ddefa00000000"
```

### P2WSHの署名の作成

```ruby
require 'bitcoin'
Bitcoin.network = :testnet3

# 2-of-2のマルチシグのredeem script
redeem_script = Bitcoin::Script.from_string('2 0303f4c425fe2bb1a15bf44010014c74d014bf1268dd43aadab0cd79b042bfaf2f 03d9cd29752768733c4d497b63b3f72c3e58f9927c6fc5912468e4536f98d7821d 2 OP_CHECKMULTISIG')

script_pubkey = Bitcoin::Script.to_witness_p2sh_script(Bitcoin.sha256(redeem_script.to_payload.bth)).bth
=> '0020b271acf6e944c13352405bf1c101d5531a208d1c3e4bef15b61f0717bd95e2bd'
```

```ruby
require 'bitcoin'
Bitcoin.network = :testnet3

# マルチシグの各公開鍵に対応する秘密鍵
key_a = Bitcoin::Key.from_base58('cVXseE36PXUwCu7xT4kbhjibVajy2RW4VmoT3b2eq2dNrqy19mcn')
key_b = Bitcoin::Key.from_base58('cMz1VdDkfTMXEJq9s7njw2BRXgF7o1CqaWnBtNgmkqSkjGW7huW5')

# P2WSHのScriptPubkey宛の送金アウトプット（0番目のアウトプット）を含むトランザクション
prev_tx = Bitcoin::Protocol::Tx.new('0100000001b090eb4d4233616d8f00ff830bdc460b4559f7577b8e99362b0a96f67ef713a00000000000ffffffff0150c3000000000000220020b271acf6e944c13352405bf1c101d5531a208d1c3e4bef15b61f0717bd95e2bd00000000'.htb)

# P2WSHのScriptPubkeyを使用するトランザクションを作成
tx = Bitcoin::Protocol::Tx.new
tx.add_in(Bitcoin::Protocol::TxIn.from_hex_hash(prev_tx.hash, 0))
tx.add_out(Bitcoin::Protocol::TxOut.value_to_address(40000, 'mt1hZLajqyc63NkWy7qvgiuum5nuTBdVZ6'))

# Segwit用の新しいフォーマットで署名対象のダイジェストを生成(ロックされているコインの量も必要になります。)
sig_hash = tx.signature_hash_for_witness_input(0, prev_tx.out[0].pk_script, prev_tx.out[0].value, redeem_script.to_payload)

# マルチシグに使われている各鍵で署名を生成
sig1 = key_a.sign(sig_hash) + [Bitcoin::Script::SIGHASH_TYPE[:all]].pack("C")
sig2 = key_b.sign(sig_hash) + [Bitcoin::Script::SIGHASH_TYPE[:all]].pack("C")

# 生成した署名値とredeem scriptをwitness領域にセット(script_sigにはセットしない)
tx.in[0].script_witness.stack << ''
tx.in[0].script_witness.stack << sig1
tx.in[0].script_witness.stack << sig2
tx.in[0].script_witness.stack << redeem_script.to_payload

# 生成した署名が有効か検証(ロックされているコインの量も必要になります。)
tx.verify_witness_input_signature(0, prev_tx, prev_tx.out[0].value)
=> true

# Segwitのシリアライゼーションフォーマットのペイロードを生成
tx.to_witness_payload.bth
=> '010000000001010cd3b20630a2b31884adf205ac80e617ba5ff8ffff872b89b772a826b8033ff60000000000ffffffff01409c0000000000001976a9148911455a265235b2d356a1324af000d4dae0326288ac040047304402202b1caf17a0a889f2dfb6e0d103ac5c88283852b72930f9d05672a073334677bb02206bc5ca5e51c3313456339d513d2d923784f2ddcf59af158a88773d1411d49d970147304402206eb8d4309cb803a69d5913b25e8df526ab1d4dd4c238d4b5dbf950bd0bfbb217022041880c51d85fe7e0448bbb26d8ce837c390b965c4ceda92f3c128b89c8857c68014752210303f4c425fe2bb1a15bf44010014c74d014bf1268dd43aadab0cd79b042bfaf2f2103d9cd29752768733c4d497b63b3f72c3e58f9927c6fc5912468e4536f98d7821d52ae00000000'
```

