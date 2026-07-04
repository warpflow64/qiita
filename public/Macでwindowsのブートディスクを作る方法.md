---
title: MacでWindowsのブートディスクを作る方法
tags:
  - Mac
  - Windows
  - wimlib
  - Windows11InstallationAssistant
private: true
updated_at: '2026-07-05T02:56:55+09:00'
id: 449084f3d235e8ada9d7
organization_url_name: null
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---

# 参考文献
@a5hr(Daisuke Ashihara)
[MacからUSBブート可能なWindows10インストールメディアを作成するのがハードル高かった](https://qiita.com/a5hr/items/8c0f87a11000e77e1795)

# TL;DR
- Mac や Linux では Windows11InstallationAssistant が使えない
- ISOファイルから書き込むにもbalenaEtcherなどでは 4GB 問題でできない
- wimlib と言うツールを使えばいける！

# 皆さんはWindowsのブートディスクをどう作りますか？

私はいつも Mac や Linux を使って生活をしています。それこそWindowsを触ることなんてここ1ヶ月で2回くらいでした。
ただ、不意にWindowsを使わなければならないターンといのは来ると思います。
ですが、Windowsのブートディスクを作るのはただならぬ努力が必要なんです！！
それを今回は備忘録的に書いていきたいと思います。

## Windows11InstallationAssistant の存在
基本的にWindowsのインストールメディアを作るときにお世話にあるソフトでMicrsoft公式から頒布されいてるソフトで"Windows"なら簡単にUSBやCDをWindowsのインストールメディアにできます。
 **ですが、これはWindows専用です**
なのでLinuxやMacでやる方法を探していました。

# やり方
## 用意するもの
- USBメモリー 32GB推奨
- PC(Mac)
- Windowsのiso [ダウンロードページ](https://www.microsoft.com/ja-jp/software-download/Windows11)

## Mac wimlib のインストール
推奨コマンド
```zsh
brew install wimlib
```

## diskutilでの操作

### USBのディスク番号を確認
```zsh
diskutil list
```

### USBをFAT32/GPTでフォーマット(diskNは自分の番号に置き換え)
```zsh
diskutil eraseDisk MS-DOS "WINUSB" GPT /dev/diskN
```

### ISOをマウント(isoファイルは自分のisoファイルに置き換え)
```zsh
hdiutil mount ~/Downloads/Win11_24H2_Japanese_x64.iso
```

## rsyncコマンドで 4GB 未満のファイルの移動
### install.wim以外を全部コピー(ボリューム名は実際の表示に合わせる)
```zsh
rsync -avh --progress --exclude=sources/install.wim /Volumes/CCCOMA_X64FRE_JA-JP_DV9/ /Volumes/WINUSB/
```

## wimlib で install.wimを4GB未満に分割してコピー
```zsh
wimlib-imagex split /Volumes/CCCOMA_X64FRE_JA-JP_DV9/sources/install.wim /Volumes/WINUSB/sources/install.swm 3800
```

# お疲れ様でした！！！
待機時間が多くて大変だったと思いますが、お疲れ様でした。

# あとがき
自分はこのやり方を Claude 先輩に聞いてやったのですが、この記事を書いている時に一番最初に書いた参考の方の記事を発見しました。
そしてその方の記事にも参考になった記事がありました。たぶんこういうデータを学習したおかげで Claude が自分にこれをやれと言ってくれたんだと思います。直接ではないにしろ本当にお世話になりました。
 **ありがとうございます** せめてもとして参考文献を最初に書かせていただきました。
