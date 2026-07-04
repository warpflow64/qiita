---
title: Mac で Windows のブートディスクを作る方法
tags:
  - Mac
  - Windows
  - wimlib
  - Windows11InstallationAssistant
private: false
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
- ISO ファイルから書き込むにも balenaEtcher などでは 4GB 問題でできない
- wimlib と言うツールを使えばいける！

# 皆さんは Windows のブートディスクをどう作りますか？

私はいつも Mac や Linux を使って生活をしています。それこそ Windows を触ることなんてここ1ヶ月で2回くらいでした。
ただ、不意に Windows を使わなければならないターンといのは来ると思います。
ですが、 Windows のブートディスクを作るのはただならぬ努力が必要なんです！！
それを今回は備忘録的に書いていきたいと思います。

## Windows11InstallationAssistant の存在
基本的に Windows のインストールメディアを作るときにお世話にあるソフトで Micrsoft 公式から頒布されいてるソフトで "Windows" なら簡単に USB や CD を Windows のインストールメディアにできます。
 **ですが、これは Windows 専用です**
なので Linux や Mac でやる方法を探していました。

# やり方
## 用意するもの
- USBメモリー 32GB推奨
- PC(Mac)
- Windowsのiso [ダウンロードページ](https://www.microsoft.com/ja-jp/software-download/Windows11)

## Mac wimlib のインストール
インストールコマンド
```zsh
brew install wimlib
```

## diskutil での操作

### USB のディスク番号を確認
```zsh
diskutil list
```

### USB を FAT32/GPT でフォーマット( diskN は自分の番号に置き換え)
```zsh
diskutil eraseDisk MS-DOS "WINUSB" GPT /dev/diskN
```

### ISO をマウント( iso ファイルは自分の iso ファイルに置き換え)
```zsh
hdiutil mount ~/Downloads/Win11_24H2_Japanese_x64.iso
```

## rsync コマンドで 4GB 未満のファイルの移動
### install.wim 以外を全部コピー(ボリューム名は実際の表示に合わせる)
```zsh
rsync -avh --progress --exclude=sources/install.wim /Volumes/CCCOMA_X64FRE_JA-JP_DV9/ /Volumes/WINUSB/
```

## wimlib で install.wim を 4GB 未満に分割してコピー
```zsh
wimlib-imagex split /Volumes/CCCOMA_X64FRE_JA-JP_DV9/sources/install.wim /Volumes/WINUSB/sources/install.swm 3800
```

# お疲れ様でした！！！
待機時間が多くて大変だったと思いますが、お疲れ様でした。

# あとがき
自分はこのやり方を Claude 先輩に聞いてやったのですが、この記事を書いている時に一番最初に書いた参考の方の記事を発見しました。
そしてその方の記事にも参考になった記事がありました。たぶんこういうデータを学習したおかげで Claude が自分にこれをやれと言ってくれたんだと思います。直接ではないにしろ本当にお世話になりました。
 **ありがとうございます** せめてもとして参考文献を最初に書かせていただきました。
