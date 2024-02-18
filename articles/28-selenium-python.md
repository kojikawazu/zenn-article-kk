---
title: "Seleniumを使ったWebアプリケーションテストの自動化"
emoji: "⚙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["selenium", "python", "chrome"]
published: false
---

# はじめに

Webアプリケーションテストで[Selenium](https://www.selenium.dev/ja/documentation/)を学ぶ機会がありました。
手動な画面操作を自動化し、テストの自動化へ紐づけるのは面白かったので共有します。

# Seleniumとは

Seleniumは、Webアプリケーションのテストを自動化するためのオープンソースツールセットです。主要なコンポーネントは以下の通りです。

- **Selenium WebDriver**: ブラウザをプログラムから直接制御し、ウェブページ上の要素を操作します。
- **Selenium IDE**: テストケースの記録と再生を行うブラウザ拡張機能です。
- **Selenium Grid**: テストスクリプトを複数のブラウザやOS上で並行して実行します。

Seleniumは多言語に対応しており、開発者は既に使用しているプログラミング言語でテストスクリプトを作成できます。

:::message
Seleniumの最近のバージョンは、**Python 3.6以降**での使用を想定しています。
:::

# Selenium動作環境の構築

:::message
WSL2を使用してWindows上のUbuntuでSeleniumを動かします。
他環境はインターネットで調査の方よろしくお願いします。
:::

## ステップ1: Pythonのインストール

UbuntuにはPythonがプリインストールされていますが、pipを別途インストールします。

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

次にSeleniumパッケージをインストールします。
以下コマンドを実行します。

```bash
# Seleniumのインストール
pip3 install selenium

# Seleniumの確認
pip3 show selenium
```

## ステップ3: Google Chromeのインストール

Ubuntu上でChromeを動かす場合に使用します。その為、Google Chromeをインストールします。

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
WSL2のUbuntu上でChromeを立ち上げると文字化けします。[^1]
その対策として、日本語パッケージ、日本語のフォントを追加します。

```bash
sudo apt install language-pack-ja
sudo apt install fonts-ipafont
sudo apt install fonts-ipaexfont
```
:::

## ステップ4: ChromeDriverのダウンロード

ChromeDriverをインストールします。Google Chromeのバージョンに合わせてバージョンを[ChromeDriverWebサイト](https://googlechromelabs.github.io/chrome-for-testing/#stable)から探し、インストールを行います。

```bash
# Chrome Driverのダウンロード
# ダウンロードURLをChromeバージョンに合わせたChrome Driverバージョンにしてください
wget https://chromedriver.storage.googleapis.com/121.0.6167.184/chromedriver_linux64.zip
# ドライバーの解凍
unzip chromedriver-linux64.zip
cd chromedriver-linux64
# コマンドを配置
sudo mv chromedriver /usr/local/bin/
# コマンド確認
which chromedriver

# (後処理)
cd ../
rm -rf chromedriver-linux64
```

## ステップ5: Chrome自動起動確認

Pythonコードを書いて、Chromeを自動起動してみます。
今回は自動起動後、10秒待ち閉じるだけのコードとなります。

```py:main.py
from selenium import webdriver
import time

# サービスの初期化
options = webdriver.ChromeOptions()
options.add_experimental_option('excludeSwitches', ['enable-logging'])
options.add_argument("--no-sandbox")
options.add_argument("--lang=ja")

# chrome driverの初期化
driver = webdriver.Chrome(options=options)

# Google Chromeへアクセス
driver.get('https://www.google.com/')

# 10秒待つ
time.sleep(10)
# 閉じる
driver.quit()
```

## ステップ6: 結果確認

Pythonコードを実行してみます。
自動でChromeが起動し、自動で閉じたら成功です。

```bash
python3 main.py
```

![Chrome結果](https://storage.googleapis.com/zenn-user-upload/0dcaf6c1d6c0-20240218.png)

# Seleniumを使ったユニットテストを実施してみる

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
    
    # テスト前準備
    def setUp(self):
        # サービスの初期化
        self.options = webdriver.ChromeOptions()
        self.options.add_experimental_option('excludeSwitches', ['enable-logging'])
        self.options.add_argument("--no-sandbox")

        # chrome driverの初期化
        self.driver = webdriver.Chrome(options=self.options)

        # アクセス
        self.driver.get('https://tutorialsninja.com/demo/index.php?route=product/search')
        # 画面最大化
        self.driver.maximize_window()

    def test_search_successed(self):
        """商品検索機能が正しく動作するかテストします。
        
        検索ボックスにキーワードを入力し、結果が期待通りに表示されることを確認します。
        """
        
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
        # 10秒待つ
        time.sleep(10)
        # 閉じる
        self.driver.quit()

if __name__ == "__main__":
    unittest.main()
```

![Chrome確認2](https://storage.googleapis.com/zenn-user-upload/1d4b61d5bb82-20240219.png)

![テスト結果](https://storage.googleapis.com/zenn-user-upload/303fcd68131d-20240219.png)



[^1]: [文字化け対策](https://lef237.hatenablog.com/entry/2022/12/05/163550)