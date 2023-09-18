# 自作OSデバッグに便利なボードの一覧

世の中には様々なSBC(Single Board Computer)がありますが、普段使いする場合と、自作OSをその上で走らせようとする場合では、選定基準が大きく異なります。

また、コンピューターの世界は進歩が速く、ボードの入手性や価格も日々変動しています。

ここでは、osdev-jpメンバーが個人的に自作OSに活用しているデバイスや、購入を検討しているデバイスについてまとめます。

もしほかにもおすすめのボードがありましたら、Pull Requestをお待ちしています！

## 自作OSをする上でのマシンの選定基準

- x86_64, arm64, rv64, etc...
  - アーキテクチャが異なれば、機械語も異なります。どのアーキテクチャをターゲットにOSを書くか、もしくは複数アーキテクチャをサポートするのか、すべてはOSを書くあなたの手に委ねられています！
  - x86_64は、自作OSに関わる解説も豊富で、実装例も多くあり、マシンの入手性も高い傾向にあります。しかし、命令セットが歴史的事情の塊である点は、諸手を挙げてはおすすめできないところかもしれません…。
  - arm64のノートPCやデスクトップマシンの選択肢はx86ほど多くありませんが、シングルボードコンピューターであればx86_64よりも入手性が高い可能性すらあります。
  - risc-v 64bit (rv64) のマシンは命令セットの美しさに惹かれますが、まだまだ少数派です。最近はやっとPCIスロットをもつボードが比較的安価に入手できるようになってきたので、触ってみるのも面白いかもしれません。
- UEFI / Legacy BIOS / U-boot / etc...
  - デバイスに採用されているファームウエアやブートローダーによっては、OSの起動コードを大幅に書き換える必要があるかもしれません。
  - x86系では、UEFIを採用しているものがほとんどですが、古いマシンを買う際などは、UEFIブートに対応しているかを確認したほうがよいでしょう。
  - その他のブートローダーについては、仕様が公開されているもの、ボードやベンダー固有のものなど、多様なものが存在しますので、事前に仕様が閲覧可能か、実装できそうかを確かめておく必要があります。
- 64bit / 32bit / etc...
  - アーキテクチャの項とほぼ重複するので省略(現在は64bitが主流)
- シリアルポートの有無
  - どのようなコンピューターでも、基本的にシリアルポートは存在します。（だって、ないと開発者がつらいので…。）しかし、「最終製品でも容易にアクセスできる」シリアルポートを搭載するマシンは意外と少ないものです。
  - 多くのノートPCの場合、筐体を開けずにシリアルポートを取ることは難しいでしょう。（Chromebookは[仕様が公開されている特殊なUSBケーブル](https://chromium.googlesource.com/chromiumos/third_party/hdctools/+/HEAD/docs/ccd.md#making-your-own-suzyq)をつなぐと、USB-C端子を経由してシリアルポートをとることができるものが多いですが、やはり難易度は少し高めです。ちなみにx86_64だとこの方法でもうまく取れない可能性が高いです…なんとかならないかなあ…（なんとかするんだよ！））
  - シングルボードPCやデスクトップマシンは、シリアルポートのピンがボード上には存在しているケースが多いでしょう。しかし、小型のマシンになればなるほど、はんだ付けが必要になったりと、難易度は高めになります。
  - 確実にシリアルポートのピンが生えていることがピン配置からわかるシングルボードコンピューターか、9-pinのRS232Cポートが生えているデスクトップ/ノートPCを手に入れると、便利かもしれません。
- キーボードの接続方法(PS/2, USB, etc...)
  - PS/2接続が可能なボードや、内部的にPS/2接続されているキーボードを搭載したノートPCが初心者にはおすすめです。（しかしそんなことは通常の仕様リストには載っていないのである！！）
  - USB接続しかない場合でも、ファームウエア(特にUEFI/Legacy BIOS)によっては、PS/2キーボードのエミュレーションをしてくれて、OSからはPS/2キーボードとして見える場合が存在します。
  - USBキーボードのサポートは、USBコントローラーのドライバさえ書けてしまえばそこまで難しくはありませんし汎用性があるので嬉しいですが、USBコントローラーのドライバ実装をゼロからするのはかなり大変です。覚悟してください。
  - また、PC外部に見えているUSBポートが、USBルートハブポートなのか、それともそれにぶらさがる、USBデバイスのハブのポートなのか、によっても難易度は変わります。（後者の場合、ハブ自体のドライバも実装しなければいけない。）
- ネットワークデバイスの種類
  - みなさんが書きたい/書けそうなNICを搭載したマシンを買いましょう（通常はどのNICを搭載しているかまではわからないケースも多いが…）
  - 無線LANは難しそうなのでやめておくのが無難です。Linuxですら苦労しています。…もしうまく動いたらぜひ教えてください！！！
  - 多くのPCに内蔵されている有線イーサネットのNICは、Realtek社（通称カニさん、カニのロゴマークより）のNICが多いでしょう。これは少し制御が特殊というか、仕様が微妙に見つからなかったりするので、がんばって見つけてください。
  - 仕様が公開されている上に、PCI拡張カードとして狙い撃ちで購入しやすいのがIntel社のNICでしょう。実装例もGitHubを漁ればある程度出てきます。
  - USBイーサネット、動かしたいですよねえ！！多くの場合はカニさんチップが、そうでなければAX88179のようなチップが載っていることが多いです。デバイスとの通信プロトコルに関しては、一応USB-CDC ECNとして仕様は存在するのですが、多くのNICは[RNDIS](https://learn.microsoft.com/ja-jp/windows-hardware/drivers/network/remote-ndis-communication)というMicrosoft社が公開している仕様を利用してやりとりをしているようです。ぜひ実装に挑戦してみてくださいね！(その前にまずUSBホストコントローラのドライバを書く必要がありますが…がんばれ！！！)

## 未確認だが気になっているデバイス

### AAEON UP Squared V2
```
- arch: x86_64
- firmware: UEFI BIOS
- serial: UART pin available
- usb: 3.0 (xhci)
```
- https://www.mouser.jp/ProductDetail/AAEON-UP/UPN-EHLCN2-A10-0232
- https://up-board.org/up-squared-v2/#TechSpecs
- https://www.aaeon.com/en/c/embedded-single-board-computers/
- [2011年からASUSグループの傘下に入ったらしい](https://www.aaeon.com/en/about/company-profile)、組み込み/工業向けコンピューターを作っている会社のボード。
- 公式のeShopからだと日本に発送できないっぽいので、日本での販売代理店である[Mouser Electronicsから購入](https://www.mouser.jp/c/?m=AAEON)するのがよい。（購入できること自体は2023年にhikaliumが実証済み。）
- 動作検証が終わり次第、情報を追記するのでお待ちを…。
- 電源が付属していないので注意。N6210のモデルの場合、22W消費すると同封の紙には書いてあった。12V3A(=36W)でバレルコネクタの電源が手元にあったので、それを使ったところ起動できた。
- 価格は2023-09にhikaliumが購入した際は、送料無料で4万円行かない程度だった。
- Mouserで409-EP-PS12V6A72WFJの品番で電源アダプタも売っていることを発見した(72W)。これを買うのが楽かもしれない。
- 2.5 mm Barrel Plug (2.5x5.5)
- BIOSはAMIのものがデフォルトで入っていた。EDK2に書き換えることもできるっぽい？（未検証）([GitHub](https://github.com/up-board/up-community/wiki/Firmware)を参照)

## おすすめ/動作実績のあるボードたち

### GPD Micro PC

```
- arch: x86_64
- firmware: UEFI BIOS
- serial: RS232C port available (COM2)
- usb: 3.0 (xhci)
```

- https://gpd-direct.jp/pages/gpd-micropc
- 超小型ノートPCでxHCIサポート、UEFI、シリアルポートの3つを備えるデバイスはなかなかない。
- hikaliumは古い世代(2021版ではないほう)を持っていた。ただし、バッテリーが放置していたらだめになっていたので、バッデリーを外して運用している。
- キーボードの接続が特殊で、内蔵されているキーボードを自作OSから制御できた報告例はまだない（はず）。間違っていたらPull-Requestを送ってください！
- スマホのディスプレイを流用しているのか、画面が90度回転した状態で映るため、ソフトウエア側での対処か、首を曲げるなどの対処が必要。

## 製造終了 / 入手困難

### MinnowBoard シリーズ

```
- arch: x86_64
- firmware: UEFI BIOS
- serial: UART pin available
- usb: 3.0 (xhci)
```

- https://www.minnowboard.org/minnowboard-turbot/technical-specs/
- 2020年頃までの自作OS界では、とても便利でお手頃価格のデバイスとして有名だった。
- xHCIサポート、シリアルポートあり、画面出力もある、便利なデバイスだった。
- 2023年現在は、公式経路での購入が困難になっている。転売や中古販売には十分に気をつけましょう！
