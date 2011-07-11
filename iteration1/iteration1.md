!SLIDE master
# イテレーション #1 #######################################################
## "コントローラクラスを作る"


!SLIDE bullets small
# Trema 最初の一歩 ####################################################

	@@@ ruby
	# テスト用ヘルパーライブラリの読み込み
	require File.join(File.dirname(__FILE__), "spec_helper")
	
	
	describe RepeaterHub do
	  # ここにリピータハブのスペックを書いていく
	  # そのままテストコードとして実行される
	end


* Ruby のテストフレームワーク [RSpec](http://relishapp.com/rspec) でテストを書く


!SLIDE commandline small
# Test First! #######################################################

	@@@ commandline
	$ rspec -fs -c spec/repeater-hub_spec.rb 
	/home/yasuhito/play/trema/spec/repeater-hub_spec.rb:4: uninitialized constant RepeaterHub (NameError)
	from /var/lib/gems/1.8/gems/rspec-core-2.6.3/lib/rspec/core/configuration.rb:419:in `load'
	from /var/lib/gems/1.8/gems/rspec-core-2.6.3/lib/rspec/core/configuration.rb:419:in `load_spec_files'
	from /var/lib/gems/1.8/gems/rspec-core-2.6.3/lib/rspec/core/configuration.rb:419:in `map'
	from /var/lib/gems/1.8/gems/rspec-core-2.6.3/lib/rspec/core/configuration.rb:419:in `load_spec_files'
	from /var/lib/gems/1.8/gems/rspec-core-2.6.3/lib/rspec/core/command_line.rb:18:in `run'
	  ...
	=> FAIL


!SLIDE small
# 修正 ##############################################################

	@@@ ruby
	require File.join( File.dirname( __FILE__ ), "spec_helper" )
	
	
	# 空のクラス定義を追加	
	class RepeaterHub
	end
	

	describe RepeaterHub do
	end


!SLIDE commandline
# ふたたびテスト ########################################################

	@@@ commandline
	$ rspec -fs -c spec/repeater-hub_spec.rb 
	No examples found.
	
	Finished in 0.00003 seconds
	0 examples, 0 failures


!SLIDE bullets small incremental
# Why Test First?

* OpenFlow は動作シーケンスが複雑
* 構成要素 (スイッチ、ホスト、コントローラ) も多い
* どこで何が起こっているかわかりづらい
* → そこで、ステップごとの動作テストが重要
* → Trema はこのためのテストフレームワークを提供
