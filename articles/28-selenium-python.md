---
title: "Seleniumを使ったWebアプリケーションテストの自動化"
emoji: "⚙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["selenium", "python", "chrome"]
published: false
---

# はじめに

Webアプリケーションテストで[Selenium](https://www.selenium.dev/ja/documentation/)を学ぶ機会がありました。
手動な画面操作を自動化し、テストの自動化へと活かせるので記録に残します。

# Seleniumとは

Seleniumは、Webアプリケーションのテストを自動化するためのオープンソースツールセットです。
主要なコンポーネントは以下の通りです。

- **Selenium WebDriver**: ブラウザをプログラムから直接制御し、ウェブページ上の要素を操作します。
- **Selenium IDE**: テストケースの記録と再生を行うブラウザ拡張機能です。
- **Selenium Grid**: テストスクリプトを複数のブラウザやOS上で並行して実行します。

Seleniumは多言語に対応しているため、開発者は既に使用しているプログラミング言語を活用してテストスクリプトを作成することが可能です。Python、Java、C#など、多様な言語での利用が想定されています。

:::message
Seleniumの最近のバージョンは、**Python 3.6以降**での使用を想定しています。
:::

# Selenium動作環境の構築

:::message
Windows Subsystem for Linux 2(WSL2)を使用してWindows上のUbuntu環境でSeleniumを動かします。
他の環境での構築方法については、オンラインで探してください。
:::

## ステップ1: Pythonのインストール

UbuntuにはPythonがプリインストールされていますが、pipを別途インストールします。
以下のコマンドを実行して、システムを最新の状態に更新し、pipをインストールします。

```bash
# パッケージリストを最新の状態に更新する
sudo apt update
sudo apt upgrade

# 最新のPython3をインストールする
sudo apt install python3-pip

# Pythonバージョンの確認
python3 -V
pip3 -V
```

## ステップ2: Seleniumパッケージのインストール

Seleniumを使用するには、Seleniumパッケージをインストールする必要があります。
次のコマンドを実行して、Seleniumをインストールします。

```bash
# Seleniumのインストール
pip3 install selenium

# Seleniumの確認
pip3 show selenium
```

## ステップ3: Google ChromeとWebDriverのインストール

Seleniumテストを実行するためには、ブラウザと対応するWebDriverが必要です。
以下の手順でGoogle ChromeとChrome WebDriverをインストールします。

```bash
# Google Chromeのダウンロード:
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
# パッケージのインストール
sudo dpkg -i google-chrome-stable_current_amd64.deb
# 依存関係の解決
sudo apt-get install -f
# Chromeバージョンの確認
google-chrome --version
```

:::message
WSL2のUbuntu上でGoogle Chromeを使用する際には、日本語表示の問題が生じることがあります。
これを解決するために、日本語パッケージとフォントをインストールします。[^1]

```bash
sudo apt install language-pack-ja fonts-ipafont fonts-ipaexfont
```
:::

## ステップ4: ChromeDriverのダウンロード

ChromeDriverをインストールします。Google Chromeのバージョンに合わせてバージョンを[ChromeDriverWebサイト](https://googlechromelabs.github.io/chrome-for-testing/#stable)から探し、インストールを行います。

```bash
# Chrome Driverのダウンロード
# ダウンロードURLをChromeバージョンに合わせたChrome Driverバージョンにしてください
wget https://chromedriver.storage.googleapis.com/121.0.6167.184/chromedriver_linux64.zip
# ダウンロードしたファイルの解凍
unzip chromedriver-linux64.zip
cd chromedriver-linux64
# 解凍したChromeDriverをシステムのPATHに追加
sudo mv chromedriver /usr/local/bin/
# ChromeDriverが正しくインストールされたことを確認
which chromedriver

# (後処理)
cd ../
rm -rf chromedriver-linux64
```

## ステップ5: Chrome自動起動確認

Pythonスクリプトを使用してGoogle Chromeを自動で起動し、動作を確認します。
以下のPythonコードは、Chromeを起動してGoogleのホームページにアクセスし、10秒後にブラウザを閉じます。

```py:main.py
from selenium import webdriver
import time

# WebDriverのオプションを設定
options = webdriver.ChromeOptions()
options.add_experimental_option('excludeSwitches', ['enable-logging'])
options.add_argument("--no-sandbox")  # セキュリティ上の制限を回避
options.add_argument("--lang=ja")     # 言語設定を日本語に

# ChromeDriverを使用してWebDriverのインスタンスを作成
driver = webdriver.Chrome(options=options)

# Googleのホームページにアクセス
driver.get('https://www.google.com/')

# 10秒間待機
time.sleep(10)
# ブラウザを閉じる
driver.quit()
```

## ステップ6: 結果確認

Pythonスクリプトを実行することで、Seleniumの動作確認を行います。
以下のコマンドを使用して、main.pyを実行します。自動でGoogle Chromeが起動し、指定したウェブページにアクセスした後、自動で閉じることが成功です。

```bash
python3 main.py
```

![Chrome結果](https://storage.googleapis.com/zenn-user-upload/0dcaf6c1d6c0-20240218.png)

# Seleniumを使ったユニットテストを実施してみる

以下のPythonコードは、Seleniumを使用してWebサイトの検索機能をテストするユニットテストです。このテストでは、tutorialsninjaのデモサイトを対象に、商品検索機能が期待通りに動作することを確認します。

```py
import unittest
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
import time

class SamplePageSearch(unittest.TestCase):
    """Seleniumを用いたWebサイトの検索機能テストクラス
    
    このクラスはtutorialsninjaのデモサイトでunitテストを実施しています。
    """
    
    def setUp(self):
        """テスト前の準備を行います。"""
        self.options = webdriver.ChromeOptions()
        self.options.add_experimental_option('excludeSwitches', ['enable-logging'])
        self.options.add_argument("--no-sandbox")

        # ChromeDriverを使用してWebDriverのインスタンスを作成
        self.driver = webdriver.Chrome(options=self.options)
        # サイトにアクセス
        self.driver.get('https://tutorialsninja.com/demo/index.php?route=product/search')
        # 画面最大化
        self.driver.maximize_window()

    def test_search_successed(self):
        """商品検索機能が正しく動作するかテストします。"""
        search_box = WebDriverWait(self.driver, 10).until(
            EC.presence_of_element_located((By.ID, "input-search"))
        )
        search_box.send_keys("mac" + Keys.ENTER)
        
        # 検索結果が表示されるまで待機
        WebDriverWait(self.driver, 10).until(
            EC.presence_of_element_located((By.CSS_SELECTOR, ".product-layout .product-thumb"))
        )
        
        # 検索結果が期待通りに表示されるか確認
        self.assertNotIn("There is no product that matches the search criteria.", self.driver.page_source)

    # テスト後の後処理
    def tearDown(self):
        """テスト後の後処理を行います。"""
        # 10秒待機する
        time.sleep(10)
        # ブラウザを閉じる
        self.driver.quit()

if __name__ == "__main__":
    unittest.main()
```

# テスト実行結果の確認

Seleniumを使用したテストスクリプトの実行後、以下のような結果を確認します。

1. **Chromeの自動起動と閉鎖**：Google Chromeが自動的に起動し、指定された操作を実行後に自動的に閉じることを確認します。成功すれば、Seleniumのセットアップと基本的な動作が正しく機能していることが示されます。

2. **ユニットテストの結果**：Seleniumを使用したユニットテストを実行した場合、テストが成功し、期待された動作が確認できることを示す結果が得られます。テスト結果は、正しく構成されたテストケースが期待通りに動作し、特定の機能が正確に動作していることを検証します。

## Chromeの自動起動確認

![Chrome確認2](https://storage.googleapis.com/zenn-user-upload/1d4b61d5bb82-20240219.png)

## ユニットテストの結果

![テスト結果](https://storage.googleapis.com/zenn-user-upload/303fcd68131d-20240219.png)

# さいごに

Seleniumを使ったWebアプリケーションのUIテストを行ってみました。従来の手動テストに代わり、テスト仕様書に基づく自動化されたテストの実施が、効率的かつ正確なテストに繋がると感じました。Pythonを使用しましたが、Seleniumは複数のプログラミング言語に対応しており、プロジェクトのニーズや開発者のスキルセットに応じて選択することが可能です。さらに多くのツールや言語を試しながら、プロジェクトごとに最適なテスト自動化に繋げてたらと考えます。

# 補足情報

- **文字化け対策**：WSL2のUbuntu上でChromeを使用する際に遭遇する可能性がある文字化け問題に対する対策として、日本語パッケージとフォントのインストールが推奨されます。詳細は外部リンク文字化け対策を参照してください。

[^1]: [文字化け対策](https://lef237.hatenablog.com/entry/2022/12/05/163550)