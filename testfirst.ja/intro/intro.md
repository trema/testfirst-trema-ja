!SLIDE center

# Trema チュートリアル ###################################################

<br />

## 高宮 安仁
## @yasuhito
## 2011 年 6 月 9 日

<br />

![Trema logo](trema.png)


!SLIDE bullets incremental small
# 自己紹介 ################################################################

* 高宮 安仁 (たかみや やすひと)
* HPC, クラスタ、クラウド (TSUBAME 1 @ 東工大、InTrigger etc.)
* Open Source: [Lucie](https://github.com/yasuhito/Lucie) cluster installer, [Dolly+](http://corvus.kek.jp/~manabe/pcf/dolly/index_J.htm) disk cloning tool etc.
* OpenFlow フレームワーク [Trema](https://github.com/trema/trema)


!SLIDE bullets incremental small
# 今日のゴール #############################################################

## "リピータハブを作りながら Trema フレームワークをまなぶ"

* Trema での開発の進めかた
* テストやデバッグの方法
* アーキテクチャとデザイン


!SLIDE
# Why Trema? ###########################################################


!SLIDE full-page-image

![ごちゃごちゃネットワーク](network_mess.png "ごちゃごちゃネットワーク")


!SLIDE bullets small incremental
# Why Trema? ###########################################################

* OpenFlow の開発は環境のセットアップが煩雑
* 登場人物が多い (たくさんのスイッチ、ホスト、リンク)
* それぞれがステートを持ち、
* それらの間で複雑な通信が起こる分散プログラミング
* <b>→ フレームワーク無しでは開発が大変</b>


!SLIDE
# Trema フレームワーク #################################################


!SLIDE full-page-image

![Trema フレームワーク](trema_framework.png "Trema フレームワーク")


!SLIDE bullets small
# Network エミュレーション ####################################################

* 仮想実行環境を開発マシンの一台の上に構築可能
* 仮想スイッチ: Open vSwitch
* 仮想ホスト: phost (pseudo host)
* 仮想リンク: vlink (ip command)
* すべてを Ruby オブジェクトでラップし、スクリプトから制御


!SLIDE bullets small
# Network DSL ##########################################################

	@@@ ruby
	vswitch("switch1") { datapath_id "0x1" }
	vswitch("switch2") { datapath_id "0x2" }
	vswitch("switch3") { datapath_id "0x3" }
	vswitch("switch4") { datapath_id "0x4" }
	
	vhost("host1")
	vhost("host2")
	vhost("host3")
	vhost("host4")
	
	link "switch1", "host1"
	link "switch2", "host2"
	link "switch3", "host3"
	link "switch4", "host4"
	link "switch1", "switch2"
	link "switch2", "switch3"
	link "switch3", "switch4"


!SLIDE bullets small
# テストスクリプト (Ruby only) #############################################

* コントローラのテストを記述
* テスト用ネットワーク環境の setup/teardown
* スイッチやホストのアサーションとエクスペクテーション


!SLIDE bullets small
# テストの例 ################################################################

	@@@ ruby
	# テスト例: MyController コントローラのユニットテスト
	
	network { # エミュレーション環境のセットアップ
	  vswitch("switch") { datapath_id "0xabc" }
	
	  vhost("host1")
	  vhost("host2")

	  link "switch", "host1"
	  link "switch", "host2"
	}.run(MyController) { # テストの実行
	  # エクスペクテーション
	  controller.should_receive(:packet_in)
	
	  # テストパケットの送信
	  send_packets "host1", "host2"
	}


!SLIDE bullets small
# 特徴のまとめ ##############################################################

* エミュレータとテストフレームワークの統合により、
* "普通" のユニットテスト技法
* (スタブ、モック、エクスペクテーション etc.)
* を分散プログラミング (OpenFlow) に適用できる


!SLIDE 
## 「さっそく Trema で何か作ってみよう」


!SLIDE commandline
# セットアップ ##############################################################

	@@@ commandline
	$ git clone git://github.com/trema/trema
	$ ./trema/build.rb
	  (There is no Step Three!!)


