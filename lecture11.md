# sampleコードを元にServerSpecのテストを実行する

## やったこと

* lecture05の環境を再度構築(間違えて削除してしまったため)
* サンプルアプリより上位階層の適当な場所にSeverSpec用のディレクトリを作成
```mkdir ServerSpec```
* SeverSpecのインストールにrubyのbundleインストールを実行。
```gem install serverspec```

* ServerSpecの初期設定を行う
```

$ serverspec-init
Select OS type:

  1) UN*X
  2) Windows

Select number: 1

Select a backend type:

  1) SSH
  2) Exec (local)

Select number:2

```
* EC2サーバー自体をテストするのでlocalを選択。

* spec/localhost/sample_spec.rbを編集
* 配布されているサンプルコードを記入

```
require 'spec_helper'

listen_port = 80

describe package('nginx') do
  it { should be_installed }
end

describe port(listen_port) do
  it { should be_listening }
end

describe command('curl http://127.0.0.1:#{listen_port}/_plugin/head/ -o /dev/null -w "%{http_code}\n" -s') do
  its(:stdout) { should match /^200$/ }
end

```

* etc/nginx/conf.d/配下の設定を確認し80番ポートをlistenしていることを確認
* テストするポートを80番に変更

* テスト実行
```rake spec```

![80番ポート](/lecture11-img/spec01.png)

* Nginxのlistenとsample_spec.rbを8080に変更

![8080番ポート](/lecture11-img/spec02.png)


## 感想

* 今回実行したテスト以外にもインストールされているパッケージやモジュールの確認など様々な書き方があった。

```describe package('git') do
  it { should be_installed }
end      
```

```%w{autoconf bison flex gcc gcc-c++ kernel-devel make m4}.each do |pkg|
  describe package(pkg) do
    it { should be_installed }
  end
end
```

* 一からServerSpec用のEC2を立ててSSH接続で行うといった方法もあるようで、自習としてそのあたりにも挑戦してみたいと思う.
