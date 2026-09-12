# ラストハルマゲドン PCE SCD 修正パッチ

PCエンジン SUPER CD-ROM²版『ラストハルマゲドン』Rev 6用の非公式修正パッチです。

このリポジトリにはゲーム本体、CDトラック、CHD、BIN/CUE、セーブデータを含みません。使用には、利用者自身が用意した対応版のディスクイメージが必要です。

## 修正内容

### 戻らずの塔から地上へ戻れなくなる不具合

戻らずの塔を初めて抜けたあと、物質転移器を取る前に魔界へ引き返し、もう一度塔を抜けると、地上の塔に埋まって移動できなくなる問題を修正します。

再通過時にも通常のフィールドモジュール再読込みを行うよう変更しています。

### セーブがある場合は「以前の続き」を初期選択

起動メニューで有効なセーブデータが見つかった場合、初期カーソルを「以前の続き」に合わせます。セーブデータがない場合は、従来どおり「最初から始める」が選択されます。

バックアップメモリの異常や空き容量不足に対する元のエラー処理は変更していません。

## 対応イメージ

パッチ対象は、次のRedump準拠raw BINです。CHDへ直接適用するものではありません。

| 項目 | 値 |
|---|---|
| タイトル | `Last Armageddon (Japan) (Rev 6)` |
| BINサイズ | `483310128` bytes |
| BIN SHA-256 | `8b14e639a446e342700ad19ef2b0619260cd7a998ce2720639ecdf8272e51de1` |
| BIN CRC32 | `c97c196b` |

異なるリビジョン、ISO、2048-byte/sector形式、CHDそのものには適用できません。BPSの入力チェックサムが一致しない場合は、強制適用しないでください。

## パッチ

[`patches/Last Armageddon (Japan) (Rev 6) [reload + continue default].bps`](<patches/Last Armageddon (Japan) (Rev 6) [reload + continue default].bps>)

| 項目 | 値 |
|---|---|
| BPSサイズ | `624` bytes |
| BPS SHA-256 | `947f09c80aeecbb57a57f67e4aeb9a0f8db5af595a16a116582f0c0748e2a495` |

## 適用方法

[Floating IPS](https://github.com/Alcaro/Flips)など、BPS対応パッチャーを使用します。

```text
flips --apply --exact patch.bps original.bin patched.bin
```

GUI版Floating IPSでは「Apply Patch」を選択し、BPSファイル、対応する元BIN、出力先の順に指定します。

適用後のBINは次の値になります。

| 項目 | 値 |
|---|---|
| BINサイズ | `483310128` bytes |
| BIN SHA-256 | `8b67cc40124a34eb6f238a87db323f6907fdeedda4bfed70d38ea6318e8f9d72` |
| BIN CRC32 | `411dcc8e` |

### CHDから適用する場合

1. `chdman extractcd` で所有しているCHDをBIN/CUEへ展開します。
2. 展開されたraw BINのSHA-256が対応イメージと一致することを確認します。
3. BPSをBINへ適用します。
4. 元CUEを複製し、先頭の `FILE` 行だけを修正後BINのファイル名へ変更します。トラック情報は変更しません。
5. `chdman createcd` で修正後BIN/CUEをCHDへ変換します。

例:

```text
chdman extractcd -i original.chd -o original.cue -ob original.bin
flips --apply --exact patch.bps original.bin patched.bin
chdman createcd -i patched.cue -o patched.chd
chdman verify -i patched.chd
```

`patched.cue` は `original.cue` を複製し、`FILE` 行を `FILE "patched.bin" BINARY` に変更したものを使用してください。

## 検証

- BPSを対応元BINへ適用し、生成物が修正後BINのSHA-256と完全一致することを確認済み。
- 修正後BINから作成したCHDで、`chdman verify` のRaw SHA-1／Overall SHA-1検証に成功。
- CHDを再展開したBINが、修正後BINとSHA-256で完全一致することを確認済み。
- 変更した全Mode 1セクタについてEDC/P/Q ECCを再生成し、再展開後にも検証済み。

詳細は[技術資料](docs/TECHNICAL.md)を参照してください。

## 注意事項

- 非公式パッチです。原作者、発売元、権利者、ハードウェア・エミュレータ開発者とは関係ありません。
- 元のイメージとセーブデータは必ずバックアップしてください。
- パッチ、パッチャー、変換ツールの使用は自己責任で行ってください。
