!SLIDE
# イテレーション #2 ######################################################
## "すべてのポートにパケットを送る"


!SLIDE small
# このイテレーションの目標 ##################################################

## あるポートに届いたパケットが他のすべてのポートに送られる

<br />

## テストに翻訳すると、

<br />
<br />

	@@@ ruby
	describe RepeaterHub do
	  # それ(RepeaterHub)は届いたパケットを他のすべてのポートへfloodingする
	  it "should flood incoming packets to every other port" do
	    # 中身はまだ空
	  end
	end


!SLIDE commandline
# テスト ##############################################################

	@@@ commandline
	$ rspec -fs -c spec/repeater-hub_spec.rb 
	
	RepeaterHub
	  should flood incoming packets to every other port
	
	Finished in 0.00028 seconds
	1 example, 0 failures


!SLIDE bullets small incremental
# テストの詳細化 #########################################################

* <b>"あるポートに届いたパケットが他のすべてのポートにも届く"</b> とは:

* スイッチ 1 台、ホスト 3 台があったとき (<i>Given</i>)、
* ホスト 1 が ホスト 2 にパケットを送ると (<i>When</i>)、
* ホスト 2 と ホスト 3 にパケットが届く (<i>Then</i>)


!SLIDE small
# Given ##############################################################

	@@@ ruby
	describe RepeaterHub do
	  it "should flood incoming packets to every other port" do
	    network {
	      # スイッチ 1 台
	      vswitch("switch") { dpid "0xabc" }

	      # ホスト 3 台
	      vhost("host1") { promisc "on" }
	      vhost("host2") { promisc "on" }
	      vhost("host3") { promisc "on" }
	
	      # ホストをスイッチにつなぐ
	      link "switch", "host1"
	      link "switch", "host2"
	      link "switch", "host3"
	    }
	  end
	end


!SLIDE small
# ネットワーク DSL #######################################################

	@@@ ruby
	# エミュレーション環境 {...} を作る
	network {
	  # 仮想スイッチ
	  vswitch("名前") { オプション }
	
	  # 仮想ホスト
	  vhost("名前") { オプション }
	
	  # 仮想リンク
	  link "ピア#1", "ピア#2"
	}
	

!SLIDE commandline
# テスト ###############################################################

	@@@ commandline
	$ rspec -fs -c spec/repeater-hub_spec.rb 
	
	RepeaterHub
	  should flood incoming packets to every other port
	
	Finished in 0.00244 seconds
	1 example, 0 failures


!SLIDE smaller
# Given, <b>When</b> #################################################

	@@@ ruby
	describe RepeaterHub do
	  it "should flood incoming packets to every other port" do
	    network {
	      vswitch("switch") { dpid "0xabc" }
	
	      vhost("host1") { promisc "on" }
	      vhost("host2") { promisc "on" }
	      vhost("host3") { promisc "on" }
	
	      link "switch", "host1"
	      link "switch", "host2"
	      link "switch", "host3"
	    }.run(RepeaterHub) {  # RepeaterHub を動かす
	      # host1 から host2 にパケットを送る
	      send_packets "host1", "host2"
	    }
	  end
	end


!SLIDE small
# ネットワーク DSL #######################################################

	@@@ ruby
	# エミュレーション環境上でコントローラ (ControllerClass) を起動し
	# テストコードを実行
	network {
	  ...
	}.run(ControllerClass) {
	  # テストコード
	  # テストコード
	  # テストコード
	}


!SLIDE commandline small
# テスト ###############################################################

	@@@ commandline
	$ rspec -fs -c spec/repeater-hub_spec.rb 
	
	RepeaterHub
	  should flood incoming packets to every other port (FAILED - 1)
	
	Failures:
	
	  1) RepeaterHub should flood incoming packets to every other port
	     Failure/Error: network {
	     RuntimeError:
	       RepeaterHub is not a subclass of Trema::Controller
	     # ./spec/repeater-hub_spec.rb:11
	
	Finished in 0.07034 seconds
	1 example, 1 failure


!SLIDE small
# 修正 ###############################################################

	@@@ ruby
	# Trema::Controller クラスを継承	
	class RepeaterHub < Trema::Controller
	end
	

	describe RepeaterHub do
	  it "should flood incoming packets to every other port" do
	    network {
	      vswitch("switch") { dpid "0xabc" }
	
	      vhost("host1") { promisc "on" }
	      vhost("host2") { promisc "on" }
	      vhost("host3") { promisc "on" }
	
	      link "switch", "host1"
	      link "switch", "host2"
	      link "switch", "host3"
	    }.run(RepeaterHub) {
	      send_packets "host1", "host2"
	    }
	  end
	end


!SLIDE smaller
# Given, When, <b>Then</b> ##########################################

	@@@ ruby
	describe RepeaterHub do
	  it "should flood incoming packets to every other port" do
	    network {
	      vswitch("switch") { dpid "0xabc" }
	
	      vhost("host1") { promisc "on" }
	      vhost("host2") { promisc "on" }
	      vhost("host3") { promisc "on" }
	
	      link "switch", "host1"
	      link "switch", "host2"
	      link "switch", "host3"
	    }.run(RepeaterHub) {
	      send_packets "host1", "host2"
	
	      # host2 と host3 がパケットを 1 つずつ受け取る
	      vhost("host2").stats(:rx).should have(1).packets
	      vhost("host3").stats(:rx).should have(1).packets
	    }
	  end
	end


!SLIDE commandline small
# FAIL! #############################################################

	@@@ commandline
	$ rspec -fs -c spec/repeater-hub_spec.rb 
	
	RepeaterHub
	  should flood incoming packets to every other port (FAILED - 1)
	
	Failures:
	
	  1) RepeaterHub should flood incoming packets to every other port
	     Failure/Error: vhost("host2").stats(:rx).should have( 1 ).packets
	       expected 1 packets, got 0
	     # ./spec/repeater-hub_spec.rb:24
	
	Finished in 4.18 seconds
	1 example, 1 failure


!SLIDE bullets small incremental
# 計画の修正 ###########################################################

* ...詰まった
* 次どうすればいいかわからない
* → やっぱりステップに分けて順に実装しよう
* → とりあえずこのテストは pending にする


!SLIDE smaller
# Pending ############################################################

	@@@ ruby
	describe RepeaterHub do
	  it "should flood incoming packets to every other port" do
	    network {
	      vswitch("switch") { dpid "0xabc" }
	
	      vhost("host1") { promisc "on" }
	      vhost("host2") { promisc "on" }
	      vhost("host3") { promisc "on" }
	
	      link "switch", "host1"
	      link "switch", "host2"
	      link "switch", "host3"
	    }.run(RepeaterHub) {
	      send_packets "host1", "host2"
	
	      pending( "あとで実装する" )
	
	      vhost("host2").stats(:rx).should have(1).packets
	      vhost("host3").stats(:rx).should have(1).packets
	    }
	  end
	end


!SLIDE commandline small
# あとまわし ##############################################################

	@@@ commandline
	$ rspec -fs -c spec/repeater-hub_spec.rb 
	
	RepeaterHub
	  should flood incoming packets to every other port (PENDING: あとで実装する)
	
	Pending:
	  RepeaterHub should flood incoming packets to every other port
	    # あとで実装する
	    # ./spec/repeater-hub_spec.rb:10
	
	Finished in 3.99 seconds
	1 example, 0 failures, 1 pending


!SLIDE full-page-image

![シーケンス図](sequence.jpg "シーケンス図")
