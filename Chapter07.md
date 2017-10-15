# ノードとビットコインネットワーク

## ハンドシェイク

```ruby
require 'bitcoin'
require 'eventmachine'

Bitcoin.network = :testnet3

class Connection < EM::Connection

  attr_reader :host, :port, :parser

  def initialize(host, port)
    @host = host
    @port = port
    # メッセージのパーサー
    @parser = Bitcoin::Protocol::Parser.new( self )
  end

  # コネクション確立直後によばれる初期化処理
  def post_init
    puts "connected."
    on_handshake_begin
  end

  # リモートノードからデータを受信した際のハンドラ
  def receive_data(data)
    parser.parse(data)
  end

  # ハンドシェイクの開始
  def on_handshake_begin
    puts "handshake begin."
    # リモートノードにバージョンメッセージを送信
    params = {block: 0, from: "127.0.0.1:18333", nonce: Bitcoin::Protocol::Uniq, to: "#{host}:#{port}",
              user_agent: "/bitcoin-ruby:#{Bitcoin::VERSION}/", version: 70015, time: Time.now.tv_sec}
    send_data(Bitcoin::Protocol.version_pkt(params))
  end

  # リモートノードからversionメッセージを受信
  def on_version(version)
    puts "receive version. Version:#{version.version}, UserAgent: #{version.user_agent}"
    # リモードノードにverackメッセージを送信
    send_data( Bitcoin::Protocol.verack_pkt )
  end

  # リモートノードからverackメッセージを受信
  def on_verack
    puts "receive verack. handshake complete."
  end

end

EM.run do
  # ローカルのBitcoin Coreのtestnetに接続
  EM.connect('localhost', 18333, Connection, 'localhost', 18333)
end
```

## SPVを効率的に実現するブルームフィルタ

### ブルームフィルタ

#### ハッシュ関数の実装


```ruby
require 'murmurhash3'

class BloomFilter

  SEED_BASE = 0xFBA4C795

  attr_reader :bits
  attr_reader :hash_funcs
  attr_reader :tweak

  def initialize(n, k, tweak)
    @bits = Array.new(n, 0) # ビット列
    @tweak = tweak # 乱数
    @hash_funcs = k # ハッシュ関数の数
  end

  # フィルタに要素を追加
  def add(data)
    calc_index(data).each do |i|
      bits[i] = 1
    end
  end

  # フィルタに要素が登録されているか確認
  def contains?(data)
    calc_index(data).map{|i|bits[i]}.find{|i| i == 0}.nil?
  end

  private

  def calc_index(data)
    list = []
    hash_funcs.times do |i|
      seed = (i * SEED_BASE + tweak) & 0xffffffff
      hash = MurmurHash3::V32.str_hash(data, seed)
      list << hash % bits.length
    end
    list
  end
end
```

```ruby
filter = BloomFilter.new(10, 2, 123456) # 123456はtweak

# フィルタに要素を追加
filter.add('bitcoin')
filter.add('monacoin')

# フィルタを検索
filter.contains?('bitcoin')
=> true

filter.contains?('litecoin')
=> false

filter.contains?('bitcoin4')
=> true
```
