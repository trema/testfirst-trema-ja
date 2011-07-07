!SLIDE center

# Trema チュートリアル ##########################################################

<br />

## 高宮 安仁
## @yasuhito
## 2011 年 6 月 9 日

<br />

![Trema logo](trema.png)


!SLIDE bullets incremental
# 誰ですか? ###############################################################

* 高宮 安仁 (たかみや やすひと)
* HPC, クラスタ、 グリッド、 クラウドを少々
* OpenFlow フレームワーク [Trema](https://github.com/trema/trema)


!SLIDE bullets incremental
# 今日のゴール #############################################################

## リピータハブを作りながら
## Trema フレームワークをひととおり使ってみる


!SLIDE
# Why Trema? ###########################################################


!SLIDE full-page-image

![ごちゃごちゃネットワーク](network_mess.png "ごちゃごちゃネットワーク")


!SLIDE bullets small incremental
# Why Trema? ###########################################################

## OpenFlow の開発は、

* 環境のセットアップが煩雑
* 登場人物が多い (スイッチ、ホスト、コントローラ)
* その間で起こる通信も複雑
* → フレームワークによるヘルプが必要


!SLIDE full-page-image

![Trema フレームワーク](trema_framework.png "Trema フレームワーク")


!SLIDE bullets small
# Network エミュレーション ####################################################

* 仮想実行環境を開発マシンの上に構築
* 仮想スイッチ: Open vSwitch
* 仮想ホスト: phost (pseudo host)
* 仮想リンク: vlink (ip command)
* すべてを Ruby オブジェクトでラップ


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
# テストスクリプト ###########################################################

* コントローラのテストを記述
* テスト環境の setup/teardown
* テストパケットの送信
* スイッチやホストをスタブ/モック化
* スイッチやコントローラ、ホストに対するアサーションと
* エクスペクテーション


!SLIDE bullets small
# テストスクリプト ###########################################################

	@@@ ruby
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


!SLIDE commandline
# セットアップ ##############################################################

	@@@ commandline
	$ git clone git://github.com/trema/trema
	$ ./trema/build.rb
	  (There is no Step Three!!)


!SLIDE 
## 「さっそく Trema で何か作ってみよう」
