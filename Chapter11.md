# スケーラビリティへの対応

## マイクロペイメントチャネル

### 単方向マイクロペイメントチャネルのプログラム例

```
$ irb
>> require 'bitcoin'
>> require 'openassets'
>> Bitcoin.network = :testnet3

>> api = OpenAssets::Api.new({
       network:             'testnet',
       provider:           'bitcoind',
       cache:            'testnet.db',
       dust_limit:                600,
       default_fees:            10000,
       min_confirmation:            1,
       max_confirmation:      9999999,
       rpc: {
         user:                  'xxx',
         password:              'xxx',
         schema:               'http',
         port:                  18332,
         host:            'localhost',
         timeout:                  60,
         open_timeout:             60 }
   })

#1 オープニングトランザクションの作成
>> tx_fee          = 50000
>> deposit_amount  = 1000000000

>> p2sh_script, redeem_script =  Bitcoin::Script.to_p2sh_multisig_script(2, alice_key.pub, bob_key.pub)
>> multisig_addr = Bitcoin::Script.new(p2sh_script).get_p2sh_address
>> opening_tx = api.send_bitcoin(alice_key.addr, deposit_amount, multisig_addr, tx_fee, 'signed')	#ブロードキャストは払い戻し用トランザクションの作成後に行う

#2 アリスが払い戻しトランザクションを作成
>> refund_tx = Bitcoin::Protocol::Tx.new

>> opening_tx_vout  = 0
>> block_height  = api.provider.getinfo['blocks'].to_i	#現在のブロック高を取得
>> locktime      = block_height + 100

>> refund_tx_in = Bitcoin::Protocol::TxIn.from_hex_hash(opening_tx.hash, opening_tx_vout)
>> refund_tx.add_in(refund_tx_in)

>> refund_tx_out = Bitcoin::Protocol::TxOut.value_to_address(deposit_amount, alice_key.addr)
>> refund_tx.add_out(refund_tx_out)

>> refund_tx.in[0].sequence = [0].pack("V")
>> refund_tx.lock_time = locktime

#3 ボブが払い戻し用トランザクションに署名
>> sig_hash = refund_tx.signature_hash_for_input(0, redeem_script)
>> script_sig = Bitcoin::Script.to_p2sh_multisig_script_sig(redeem_script)

>> script_sig_1 = Bitcoin::Script.add_sig_to_multisig_script_sig(bob_key.sign(sig_hash), script_sig)
>> refund_tx.in[0].script_sig = script_sig_1

#4 アリスがボブから払い戻し用トランザクションを受け取り、ボブの署名が正しくされているか検証
>> refund_tx_copy = refund_tx

>> script_sig_2 = Bitcoin::Script.add_sig_to_multisig_script_sig(alice_key.sign(sig_hash), script_sig_1)	#アリスの署名を追加
>> script_sig_3 = Bitcoin::Script.sort_p2sh_multisig_signatures(script_sig_2, sig_hash)	#署名の順番を並び替え
>> refund_tx_copy.in[0].script_sig = script_sig_3

>> if refund_tx_copy.verify_input_signature(0, opening_tx)
     refund_tx = refund_tx_copy	#署名の検証が正しい場合は、署名を確定する
   end

#5 オープニングトランザクションのブロードキャスト
>> api.provider.send_transaction(opening_tx.to_payload.bth)

#6 コミットメントトランザクションの作成
>> commitment_tx = Bitcoin::Protocol::Tx.new

>> amount_to_bob	   = 300000000
>> amount_to_alice    = deposit_amount - amount_to_bob - tx_fee

>> commitment_tx_in = Bitcoin::Protocol::TxIn.from_hex_hash(opening_tx.hash, opening_tx_vout)
>> commitment_tx.add_in(refund_tx_in)

>> commitment_tx_out_1 = Bitcoin::Protocol::TxOut.value_to_address(amount_to_bob, bob_key.addr)	#ボブへの支払い
>> commitment_tx_out_2 = Bitcoin::Protocol::TxOut.value_to_address(amount_to_alice, alice_key.addr)	#アリスへのお釣り
>> commitment_tx.add_out(commitment_tx_out_1)
>> commitment_tx.add_out(commitment_tx_out_2)

#7 アリスがコミットメントトランザクションに署名
>> commitment_sig_hash = commitment_tx.signature_hash_for_input(0, redeem_script)
>> commitment_script_sig = Bitcoin::Script.to_p2sh_multisig_script_sig(redeem_script)

>> script_sig_a = Bitcoin::Script.add_sig_to_multisig_script_sig(alice_key.sign(commitment_sig_hash), commitment_script_sig)
>> commitment_tx.in[0].script_sig = script_sig_a

#8 ボブがアリスからコミットメントトランザクションを受け取り、アリスの署名が正しくされているか検証
>> commitment_tx_copy = commitment_tx

>> script_sig_b = Bitcoin::Script.add_sig_to_multisig_script_sig(bob_key.sign(commitment_sig_hash), script_sig_a)	#ボブの署名を追加
>> script_sig_c = Bitcoin::Script.sort_p2sh_multisig_signatures(script_sig_b, commitment_sig_hash)	#署名の順番を並び替え
>> commitment_tx_copy.in[0].script_sig = script_sig_c
>> commitment_tx_copy.verify_input_signature(0, opening_tx)

>> if commitment_tx_copy.verify_input_signature(0, opening_tx)
     commitment_tx = commitment_tx_copy	#署名の検証が正しい場合は、署名を確定する
   end

#9 コミットメントトランザクションのブロードキャスト
>> api.provider.send_transaction(commitment_tx.to_payload.bth)
```
