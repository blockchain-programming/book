# ブロックチェーン

### ブロックの識別子

```json
$ bitcoin-cli getchaintips
[
  {
    "height": 467961,
    "hash": "0000000000000000005fb29471da62846b06fcc982cdd21ac8ae7fb235b691ac",
    "branchlen": 53,
    "status": "headers-only"
  }, 
  {
    "height": 467908,
    "hash": "000000000000000001808e86f1c9af5ab8d4903e4ddc6424f1bff197b7520d0f",
    "branchlen": 0,
    "status": "active"
  }
]
```


```
$ bitcoin-cli getblockhash 467908
000000000000000001808e86f1c9af5ab8d4903e4ddc6424f1bff197b7520d0f
```

### ブロックの本体を得る

```json
$ bitcoin-cli getblock 000000000004fc60b9bd5906556ea30dde31bdd1ffb3e8e40bcfc9896004faa9
{
  "hash": "000000000004fc60b9bd5906556ea30dde31bdd1ffb3e8e40bcfc9896004faa9",
  "confirmations": 114533,
  "strippedsize": 18585,
  "size": 18585,
  "weight": 74340,
  "height": 1087530,
  "version": 536870912,
  "versionHex": "20000000",
  "merkleroot": "b3fcd1d3666ee3a62618aae79b93eae4e14638cc98e8e2c9f10edbceecb4494b",
  "tx": [
    "9556f2d49f797252e16ad03fe649ba0e3c70a2745e2e3f8eecd13244d94fb207", 
    "803b91f42e7bfd4bc2097cebc3854a37844b5f47abb3be307d4f877cb059dfd0", 
    "3b14d39ca1e8c2a70f0a423e833bd454eaee31ea4f5609c57a4d605244946ca3", 
    "bad7d593c408eae7d5b50c40a1353ca1b2db80fd4bf9b73ae712a2bf9b1d44d3", 
    "d451b694a856265faaee7d3d48118c4e57c68c362f5fed161cacb9358bfa65f8", 
    "9de7ee1db2f268819666dd32d9373bc637370bf0ce85a63dafe39e627b0b6cdf", 
    "a5b002db81f1c09eab197cb2cb1d1cecf193ac6677fa78f26dadf54e09a7042f", 
    "c3bafa0c5cea0a641ca278db832eef30fec16f69cfe3c2dce958f242409c014a", 
    "bedae8f51726e7ed59bf8bae7a4f257edaeb64d794199834180eca85c8ca026c", 
    "de2c06f4cb60950d5b72ec37a31e6d6d47852c53abd05ebe6cf090de2db814ec", 
    "7865c689b63531eaf4eca9aa6f8501a05de8435207d829d3c99da84430a1df18", 
    "6c556d773ada9b6c1cf8b00d7e60fdd30df3882340e277f2d9e553f03bc108e5", 
    "cb5ce4fa100c5483082672009a919237172068cc2d0c7e8b585f7eeb5d836183", 
    "3f2a45c80d8cf24fea9fb7a6a6bad7f848676690bf7982ff98c1884adf532382", 
    "6ef2e72d36f7927d93ed2f880df4469ef9ed745e60b3ddfcb2e991ff04e20c73", 
    "5235324c70538022b8d9f7f3a51647cad83ba404c46830ac95b22a425c08ecf8", 
    "90bafb624a0dace70c99063b27f75efc2b867d0a4148f63f2dd7c44e50dbf664", 
    "8884dbd545e8998594d1f143b298ea1234e9c895897dd6ab119c9866ceeda1f4", 
    "7e40c1fc794028cacaffe9245991a31ebb2328a051f68a1108f0e6a7d1b7e797", 
    "40820e1039b84c3901956c3b6060bc18430b10fd4c2f414fc5d8e31a35c81e4a", 
    "569772729262d50a2f14836560f46b141618d7e87a89b8434800e687fadb371d", 
    "1fd7c930c23d53661d92b0b86754ade22ad34a499965a5570d1f48a607766c47", 
    "5c398761b779790ef3d49354a3b04ebf9645a765b1e496f87f51d02389d27c99", 
    "c4c3928a402147b27da4bba8a860681e8d322530c2ce182e7c2d87af055a3765", 
    "a0b78858f9303532495cb00579d87284a24b8cd1864e1650ecee0eaa64559a11", 
    "f9c4e5a83dd92c6a70c7a7abbc281b05c90488170827ba66eeebc08c9fe44cb6", 
    "75131cd6be0f350c5d312a40cc687e20473d6bfa0bf098fa82fd29d2a296a547", 
    "3e2b5a8e4a59dbe4cc7f04ecc47dc2346733c205c1f9d448b84b94c925f592ad", 
    "9b3aa88a4ae994f51cbff9ca07e0c4e854719ec697e54266a72693e2da86a260", 
    "346cca552be9b292e8dc01a4322d8596c943f94b60d49320ff97349ab3328c06", 
    "6f5ea126e5a633d6ce0c719f067151030f185c6432cf9e9b5f778a410c4c7211", 
    "3efd6b1c64e9887df87b4f130b38630c96d341e857f5646e7ab81fd3a4eb6703", 
    "1fb4925268e86e1c20894f920d0cb23c2685dff1e7792c80d8290e3f36d4008f", 
    "5d10bfb32188b75bedd47a23690cd1ffdacf900051a6c24995303106ae5f3c87", 
    "34c8300ea04b06cc07737f1f3a67aa7854fcb275b725683f2feb69c56241e55f", 
    "8295be39b6c38e734e92d58c85100d238f956370da2ebf0d9b91fb7e3da1b0a6", 
    "b893468e8482ebae38f2b4c26ec7c08420b66619a33852be4dd9eecf1971367c", 
    "d84ad309b6d4b1252c5c13e1bb890e0baa25405871222e28b888ca0e069e51bf", 
    "1aebcb5f98f586619afbcec21012565a9ae4e6770c0bab95e3c1d407fb98977f", 
    "6ea261976c63ccfaa57f2e74df5358ae0888758fdc67ff1d3c0f9da4bc84f32c", 
    "9636511008f069630cd731920deabfc20ff07f7c0e557f1f5b3a7fceda3569a9", 
    "46bab973bf381dc7c4cd70fd669c11e36d93ed42a4c22399849d476a93067a34", 
    "09bef05054c8f01c3b48561780022a3b2c0bd59b7776e9eb2e33ced7a9992cfa", 
    "54e2280cec37e4827e0d178d9bc09a7e28550e0cfbd8b8e67c11776ffac8f2a3", 
    "3205375e00315850da938e2aaa6144d098441aa9b67b7ae3457994cfa167eb56", 
    "1982cd168706f105f17bda981cf386b0482fbeac26b9a0068fb365aed1b895c1", 
    "98050e96588d68e770e6008a0af0944781cc4c1eadca5d6637aba60c8a1136f8", 
    "ba4e7e22c85b0bc6391cf1bfc65a066b656797e197bef37feb47848d2c50c029", 
    "4a3b38c56b52c2de9b538839eba51bafdaa338c9b6b8674345a685d7b3c63100", 
    "8abf83cc6d3fd14e7ac55f0959e224b6a5fe4a776ff9f4f7d0233604f690b600", 
    "b745fb1ea8bdfe6f36593e54f6dda311d2f991de99619ba6c7a0efc101d51d1a", 
    "a1c8cc9684a9f7f212cca19bbf0da2fcabaf21622ff63bd0a0828c498dc935bd", 
    "a50f2c3fb6a197772a3f7d46be019fcf025a07e573d424e725f62fdd66fdebcf"
  ],
  "time": 1486101281,
  "mediantime": 1486097753,
  "nonce": 4109938712,
  "bits": "1d00ffff",
  "difficulty": 1,
  "chainwork": "00000000000000000000000000000000000000000000001e9b25b784d24108ce",
  "previousblockhash": "00000000000005d6953af533ab31befe6006e27156ce7257337abfac24037d6b",
  "nextblockhash": "00000000000003396748a21cffdad3733d4f71faa3bb022f242f71d401d177f0"
}
```

### ブロックヘッダの構造


```json
$ bitcoin-cli getblockheader 000000000004fc60b9bd5906556ea30dde31bdd1ffb3e8e40bcfc9896004faa9
{
  "hash": "000000000004fc60b9bd5906556ea30dde31bdd1ffb3e8e40bcfc9896004faa9",
  "confirmations": 114531,
  "height": 1087530,
  "version": 536870912,
  "versionHex": "20000000",
  "merkleroot": "b3fcd1d3666ee3a62618aae79b93eae4e14638cc98e8e2c9f10edbceecb4494b",
  "time": 1486101281,
  "mediantime": 1486097753,
  "nonce": 4109938712,
  "bits": "1d00ffff",
  "difficulty": 1,
  "chainwork": "00000000000000000000000000000000000000000000001e9b25b784d24108ce",
  "previousblockhash": "00000000000005d6953af533ab31befe6006e27156ce7257337abfac24037d6b",
  "nextblockhash": "00000000000003396748a21cffdad3733d4f71faa3bb022f242f71d401d177f0"
}

$ bitcoin-cli getblockheader 000000000004fc60b9bd5906556ea30dde31bdd1ffb3e8e40bcfc9896004faa9 false
000000206b7d0324acbf7a335772ce5671e20660febe31ab33f53a95d6050000000000004b49b4eccedb0ef1c9e2e898cc3846e1e4ea939be7aa1826a6e36e66d3d1fcb3211b9458ffff001d18b0f8f4

```

#### ブロックハッシュの求め方

```ruby
require 'digest'

# Internal Byte Order によるSHA256のダブルハッシュ
def dh(data)
  Digest::SHA256.digest(Digest::SHA256.digest([data].pack("H*"))).unpack("h*")[0].reverse
end
```


```ruby
block_header="000000206b7d0324acbf7a335772ce5671e20660febe31ab33f53a95d6050000000000004b49b4eccedb0ef1c9e2e898cc3846e1e4ea939be7aa1826a6e36e66d3d1fcb3211b9458ffff001d18b0f8f4"

dh(block_header)
=> "000000000004fc60b9bd5906556ea30dde31bdd1ffb3e8e40bcfc9896004faa9"
```

### マークル木

```ruby
require 'digest'

# ビットコインでは、バイト単位で反転し、さらに逆順にするSHA-256のダブルハッシュを利用しています
def dhash(data)
 Digest::SHA256.digest(Digest::SHA256.digest([data].pack("H*").reverse )).reverse.unpack("H*")[0]
end

# マークルルートの作成（入力は、ハッシュ値のリスト）
def merkle_root(nodes)
 if nodes.size==1 then nodes[0]               # ノード数が一つになったらマークルルート
 else
   merkle_root(nodes.each_slice(2).map{|x|
   if x.size==2 then dhash(x[1]+x[0])         # ノードを2つずつ連接してハッシュ値をとる
   else dhash(x[0]+x[0])                      # 一つ残ったらコピーして２つにしてハッシュ値をとる
   end})
 end
end

TXID_LIST = [
   "9556f2d49f797252e16ad03fe649ba0e3c70a2745e2e3f8eecd13244d94fb207", 
    "803b91f42e7bfd4bc2097cebc3854a37844b5f47abb3be307d4f877cb059dfd0", 
    "3b14d39ca1e8c2a70f0a423e833bd454eaee31ea4f5609c57a4d605244946ca3", 
    "bad7d593c408eae7d5b50c40a1353ca1b2db80fd4bf9b73ae712a2bf9b1d44d3", 
    "d451b694a856265faaee7d3d48118c4e57c68c362f5fed161cacb9358bfa65f8", 
    "9de7ee1db2f268819666dd32d9373bc637370bf0ce85a63dafe39e627b0b6cdf", 
    "a5b002db81f1c09eab197cb2cb1d1cecf193ac6677fa78f26dadf54e09a7042f", 
    "c3bafa0c5cea0a641ca278db832eef30fec16f69cfe3c2dce958f242409c014a", 
    "bedae8f51726e7ed59bf8bae7a4f257edaeb64d794199834180eca85c8ca026c", 
    "de2c06f4cb60950d5b72ec37a31e6d6d47852c53abd05ebe6cf090de2db814ec", 
    "7865c689b63531eaf4eca9aa6f8501a05de8435207d829d3c99da84430a1df18", 
    "6c556d773ada9b6c1cf8b00d7e60fdd30df3882340e277f2d9e553f03bc108e5", 
    "cb5ce4fa100c5483082672009a919237172068cc2d0c7e8b585f7eeb5d836183", 
    "3f2a45c80d8cf24fea9fb7a6a6bad7f848676690bf7982ff98c1884adf532382", 
    "6ef2e72d36f7927d93ed2f880df4469ef9ed745e60b3ddfcb2e991ff04e20c73", 
    "5235324c70538022b8d9f7f3a51647cad83ba404c46830ac95b22a425c08ecf8", 
    "90bafb624a0dace70c99063b27f75efc2b867d0a4148f63f2dd7c44e50dbf664", 
    "8884dbd545e8998594d1f143b298ea1234e9c895897dd6ab119c9866ceeda1f4", 
    "7e40c1fc794028cacaffe9245991a31ebb2328a051f68a1108f0e6a7d1b7e797", 
    "40820e1039b84c3901956c3b6060bc18430b10fd4c2f414fc5d8e31a35c81e4a", 
    "569772729262d50a2f14836560f46b141618d7e87a89b8434800e687fadb371d", 
    "1fd7c930c23d53661d92b0b86754ade22ad34a499965a5570d1f48a607766c47", 
    "5c398761b779790ef3d49354a3b04ebf9645a765b1e496f87f51d02389d27c99", 
    "c4c3928a402147b27da4bba8a860681e8d322530c2ce182e7c2d87af055a3765", 
    "a0b78858f9303532495cb00579d87284a24b8cd1864e1650ecee0eaa64559a11", 
    "f9c4e5a83dd92c6a70c7a7abbc281b05c90488170827ba66eeebc08c9fe44cb6", 
    "75131cd6be0f350c5d312a40cc687e20473d6bfa0bf098fa82fd29d2a296a547", 
    "3e2b5a8e4a59dbe4cc7f04ecc47dc2346733c205c1f9d448b84b94c925f592ad", 
    "9b3aa88a4ae994f51cbff9ca07e0c4e854719ec697e54266a72693e2da86a260", 
    "346cca552be9b292e8dc01a4322d8596c943f94b60d49320ff97349ab3328c06", 
    "6f5ea126e5a633d6ce0c719f067151030f185c6432cf9e9b5f778a410c4c7211", 
    "3efd6b1c64e9887df87b4f130b38630c96d341e857f5646e7ab81fd3a4eb6703", 
    "1fb4925268e86e1c20894f920d0cb23c2685dff1e7792c80d8290e3f36d4008f", 
    "5d10bfb32188b75bedd47a23690cd1ffdacf900051a6c24995303106ae5f3c87", 
    "34c8300ea04b06cc07737f1f3a67aa7854fcb275b725683f2feb69c56241e55f", 
    "8295be39b6c38e734e92d58c85100d238f956370da2ebf0d9b91fb7e3da1b0a6", 
    "b893468e8482ebae38f2b4c26ec7c08420b66619a33852be4dd9eecf1971367c", 
    "d84ad309b6d4b1252c5c13e1bb890e0baa25405871222e28b888ca0e069e51bf", 
    "1aebcb5f98f586619afbcec21012565a9ae4e6770c0bab95e3c1d407fb98977f", 
    "6ea261976c63ccfaa57f2e74df5358ae0888758fdc67ff1d3c0f9da4bc84f32c", 
    "9636511008f069630cd731920deabfc20ff07f7c0e557f1f5b3a7fceda3569a9", 
    "46bab973bf381dc7c4cd70fd669c11e36d93ed42a4c22399849d476a93067a34", 
    "09bef05054c8f01c3b48561780022a3b2c0bd59b7776e9eb2e33ced7a9992cfa", 
    "54e2280cec37e4827e0d178d9bc09a7e28550e0cfbd8b8e67c11776ffac8f2a3", 
    "3205375e00315850da938e2aaa6144d098441aa9b67b7ae3457994cfa167eb56", 
    "1982cd168706f105f17bda981cf386b0482fbeac26b9a0068fb365aed1b895c1", 
    "98050e96588d68e770e6008a0af0944781cc4c1eadca5d6637aba60c8a1136f8", 
    "ba4e7e22c85b0bc6391cf1bfc65a066b656797e197bef37feb47848d2c50c029", 
    "4a3b38c56b52c2de9b538839eba51bafdaa338c9b6b8674345a685d7b3c63100", 
    "8abf83cc6d3fd14e7ac55f0959e224b6a5fe4a776ff9f4f7d0233604f690b600", 
    "b745fb1ea8bdfe6f36593e54f6dda311d2f991de99619ba6c7a0efc101d51d1a", 
    "a1c8cc9684a9f7f212cca19bbf0da2fcabaf21622ff63bd0a0828c498dc935bd", 
    "a50f2c3fb6a197772a3f7d46be019fcf025a07e573d424e725f62fdd66fdebcf"]

mRoot = merkle_root(TXID_LIST)
=> "b3fcd1d3666ee3a62618aae79b93eae4e14638cc98e8e2c9f10edbceecb4494b"
```

この計算結果は、上記のブロックの内容のマークルルートに一致します。

```
 "merkleroot": "b3fcd1d3666ee3a62618aae79b93eae4e14638cc98e8e2c9f10edbceecb4494b",
```

## マイニング

### マイニング処理

```ruby
  # 80バイトのブロックヘッダ
  blockheader="000000203876761537d4fb9b27a15382a21cd5d36c538d37ce632b000000000000000000c2d022b34fddf98bb3cafd834a4bd7a0264f14d30bf3d9852580fc29794677746d91255954d80118605d1964"
  
  # ブロックヘッダのハッシュ値を得る
  dhash(blockheader)
=> "000000000000000001808e86f1c9af5ab8d4903e4ddc6424f1bff197b7520d0f"
 
  # ブロックヘッダーのnonce フィールドの値のInternal Byte Orderを整数にする
  noce=[blockheader[-8..-1]].pack('H*').unpack('h*')[0].reverse.to_i(16)
=> 1679383904
```

#### コインベーストランザクションを得る

```json
$ bitcoin-cli getrawtransaction 169f6278f5dccc673a7c9b7f2432f22dc6cf14b2a5f23c9c209b3ebd6f9c0b7b
01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff1c03c42307046b9125592f426978696e2f0832b7658b20804200000000ffffffff01cfa2eb5a000000001976a914cef3550ff9e637ddd120717d43fc21f8a563caf888ac00000000
```


```json
$ bitcoin-cli decoderawtransaction 01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff1c03c42307046b9125592f426978696e2f0832b7658b20804200000000ffffffff01cfa2eb5a000000001976a914cef3550ff9e637ddd120717d43fc21f8a563caf888ac00000000
{
  "txid": "169f6278f5dccc673a7c9b7f2432f22dc6cf14b2a5f23c9c209b3ebd6f9c0b7b",
  "hash": "169f6278f5dccc673a7c9b7f2432f22dc6cf14b2a5f23c9c209b3ebd6f9c0b7b",
  "size": 113,
  "vsize": 113,
  "version": 1,
  "locktime": 0,
  "vin": [
    {
      "coinbase": "03c42307046b9125592f426978696e2f0832b7658b20804200000000",
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 15.25392079,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 cef3550ff9e637ddd120717d43fc21f8a563caf8 OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a914cef3550ff9e637ddd120717d43fc21f8a563caf888ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "1KsFhYKLs8qb1GHqrPxHoywNQpet2CtP9t"
        ]
      }
    }
  ]
}
```


```ruby
coinbase="03c42307046b9125592f426978696e2f0832b7658b20804200000000"

coinbase[2..8]
=> "c423070"
   ["c42307"].pack('h*').unpack('H*')[0].reverse.to_i(16)
=> 467908
```

```
coinbase[9..10]
=> "04"

coinbase[10..17]
=> "6b912559"
```

### マイニングの難易度


#### difficultyの表現


```ruby
  blockheader="000000203876761537d4fb9b27a15382a21cd5d36c538d37ce632b000000000000000000c2d022b34fddf98bb3cafd834a4bd7a0264f14d30bf3d9852580fc29794677746d91255954d80118605d1964"
  
difficulty_target=[blockheader[-16..-9]].pack('H*').unpack('h*')[0].reverse
=> "1801d854"
```

$ terget =  係数部 * 2**(8*(指数部 - 3)) $


```ruby
def terget(difficulty_target)
	exponent=difficulty_target[0..1].hex
	coefficient=difficulty_target[2..7].hex
	(coefficient*2**(8*(exponent-3)))
end

terget(difficulty_target)
=> 45240046586752885057924289339576851866807485277820420096
```


```ruby
require 'digest'
# SHA-256 ダブルハッシュ
def dhash(data)
  Digest::SHA256.digest(Digest::SHA256.digest([data].pack("H*"))).unpack("h*")[0].reverse
end

# ブロックヘッダのダブルハッシュと難易度ターゲットの比較
dhash(blockheader).hex < terget(difficulty_target)
=> true
```

#### getblocktemplate API

```json
$ bitcoin-cli getblocktemplate '{"jsonrpc": "1.0", "id":"curltest", "method": "getblocktemplate", "params": [] }'

{
  "capabilities": [
    "proposal"
  ],
  "version": 536870914,
  "rules": [
    "csv"
  ],
  "vbavailable": {
    "segwit": 1
  },
  "vbrequired": 0,
  "previousblockhash": "000000000000000001495cb64f4264baf2b8e6dd189c73c7b01ee083e16ea694",
  "transactions": [
    {
      "data": "010000000106c8e8bd303fec27356c0a04a4e91f71c6307dd2fdd80ad72524055664618b67010000006b4830450221008fb9cd0c2e7dfd762453f58a84a19facb44a6b8bee83bdd047ed1b83e8e8421502205c3aa141f900e2d634bbe6ec22def3873bbb554dbc1cddbd4d4db6a616aa907b01210377ac3fe26ce1325f804bae131ce1652a14df96c3227735d5aadddefc663a41f7ffffffff01c26ed71b0000000017a91481f5c7f34ff0b8c19eb33cb3a69feb1b3694a2968700000000",
      
   ...
      
  ],
  "noncerange": "00000000ffffffff",
  "sigoplimit": 20000,
  "sizelimit": 1000000,
  "curtime": 1496235485,
  "bits": "1801d854",
  "height": 469048
}
```


## ブロックチェーンからの情報収集

### mainnet における仮想通貨の流通経路の追跡

bitcoin.conf

```
mainnet=1
server=1
rest=1
txindex=1
rpcuser=<ユーザ名>
rpcpassword=<パスワード>
rpcport=8332
```

追跡プログラム

```ruby
require 'bitcoin'
require 'net/http'
require 'json'
RPCUSER = <ユーザ名>　    	# JSON RPC のためのユーザ名
RPCPASSWORD = <パスワード>	# JSON RPC のためのパスワード
HOST="localhost"			# JSON RPC のhost
PORT=8332					# ポート番号
 
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
  require 'bitcoin'
  
  # 最初のトランザクションID
  txid="ab.....................ec8c50"
  # そのトランザクションの16進データを得る
  rtx = bitcoinRPC('getrawtransaction',[txid])
  # そのトランザクションの構造を得る（ハッシュ）
  tx=bitcoinRPC('decoderawtransaction',[rtx])
  tx.keys
=> ["txid", "hash", "size", "vsize", "version", "locktime", "vin", "vout"]
  # このトランザクションのインプットのサイズを見る
  tx["vin"].size
=>  1
  tx["vin"][0].keys
=> ["txid", "vout", "scriptSig", "sequence"]
  # このインプットに送金したトランザクションIDとアウトプットの順序を求める
  ptxid=tx["vin"][0]["txid"]
  pout=tx["vin"][0]["vout"]
  # インプットに送金したトランザクションの16進形式
  prtx = bitcoinRPC('getrawtransaction',[ptxid])
  # インプットに送金したトランザクションの解析結果
  ptx=bitcoinRPC('decoderawtransaction',[prtx])
  # インプットに送金したトランザクションから見た送金先アドレス
  ptx["vout"][pout]["scriptPubKey"]["addresses"][0]
  # 最初のトランザクションの送信元のビットコインアドレス
=> "1L.....................Q7LVr"
```

```ruby
def sender_address(txid)
  rtx=bitcoinRPC('getrawtransaction',[txid])
  tx=bitcoinRPC('decoderawtransaction',[rtx])
  tx["vin"].map{|x|
    prtx=bitcoinRPC('getrawtransaction',[x["txid"]])
    pout=x["vout"]
    ptx=bitcoinRPC('decoderawtransaction',[prtx])
    ptx["vout"][pout]["scriptPubKey"]["addresses"][0]}.uniq
end

  sender_address("f5.....................085").uniq
=> ["3A.....................4AG", "3J.....................ZAN"]

```

```ruby
def prev_txids(txid)
  rtx=bitcoinRPC('getrawtransaction',[txid])
  tx=bitcoinRPC('decoderawtransaction',[rtx])
  tx["vin"].map{|x|x["txid"]}
end

  # 直前のトランザクションIDの一覧
  prev_txids("25.....................9b1a")
=> ["7c.....................f3a6", "94.....................8353", "ad.....................0c99", "71.....................839c", "93.....................07b3"]

  # コインベーストランザクションの場合
  prev_txids("24.....................07d8")
=> [nil]
```

```ruby
def trace_tx(txid,depth)
  if txid==nil or depth==0 then
  else [prev_txids(txid).map{|x|trace_tx(x,depth-1)},txid]
  end
end
```

### 仮想通貨の流通速度の測定

```ruby
blkid=bitcoinRPC('getblockhash',[469060])
blk=bitcoinRPC('getblock',[blkid])
blk["tx"].size
=> 1734
rtx=bitcoinRPC('getrawtransaction',[blk["tx"][1]])
tx=bitcoinRPC('decoderawtransaction',[rtx])
tx["vout"][0]["value"]
=> 0.075
```

```ruby
def circulation(height)
  blkid=bitcoinRPC('getblockhash',[height])
  blk=bitcoinRPC('getblock',[blkid])
  blk["tx"].map{|txid|
    rtx=bitcoinRPC('getrawtransaction',[txid])
    tx=bitcoinRPC('decoderawtransaction',[rtx])
    tx["vout"].map{|x|x["value"]}.reduce(:+)}.reduce(:+)
end

circulation(469060)
=> 10913.489625569999
```


