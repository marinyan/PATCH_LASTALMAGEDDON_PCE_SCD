# ラストハルマゲドン PCE SCD 修正パッチ

PCエンジン SUPER CD-ROM²版『ラストハルマゲドン』日本語版 Rev 6用の非公式修正パッチです。

このリポジトリにはゲーム本体、CDトラック、CHD、BIN/CUE、セーブデータを含みません。使用には、利用者自身が用意した対応版のディスクイメージが必要です。

## 修正内容

### 戻らずの塔から地上へ戻れなくなる不具合

戻らずの塔を初めて抜けたあと、物質転移器を取る前に魔界へ引き返し、もう一度塔を抜けると、地上の塔に埋まって移動できなくなる問題を修正します。

再通過時にも通常のフィールドモジュール再読込みを行うよう変更しています。

### セーブがある場合は「以前の続き」を初期選択

起動メニューで有効なセーブデータが見つかった場合、初期カーソルを「以前の続き」に合わせます。セーブデータがない場合は、従来どおり「最初から始める」が選択されます。

バックアップメモリの異常や空き容量不足に対する元のエラー処理は変更していません。

### 飛行／着地でフィールド曲が頭から再生される問題

IIボタンの「ひこう」「ちゃくち」でフィールドを再読込みしても、再生中のCD-DA位置を取得し、同じ曲をその位置から復元します。取得位置より10 CDフレーム（約0.133秒）先から再開する設定です。曲が終端へ近づいた場合は安全側で加算を省き、通常のループへ戻ります。

短い音切れは残りますが、ゲーム内で飛行／着地の往復、戦闘後、魔界復帰後、曲終端付近の再生を確認しました。この修正は下記の**統合版**だけに含まれます。

## 対応イメージ

パッチ対象は、次のRedump準拠raw BINです。CHDへ直接適用するものではありません。

| 項目 | 値 |
|---|---|
| タイトル | `Last Armageddon` |
| BINサイズ | `483310128` bytes |
| BIN SHA-256 | `8b14e639a446e342700ad19ef2b0619260cd7a998ce2720639ecdf8272e51de1` |
| BIN CRC32 | `c97c196b` |

異なるリビジョン、ISO、2048-byte/sector形式、CHDそのものには適用できません。BPSの入力チェックサムが一致しない場合は、強制適用しないでください。

## パッチ

### 統合版（塔・起動メニュー・フィールド曲、v10）

[`patches/Last Armageddon (Japan) (Rev 6) [reload + continue default + bgm resume v10].bps`](<patches/Last Armageddon (Japan) (Rev 6) [reload + continue default + bgm resume v10].bps>)

| 項目 | 値 |
|---|---|
| BPSサイズ | `1739` bytes |
| BPS SHA-256 | `f535a0c8ab4f159b12674342a03415701af77fb4edd0e2bd315e04c751536349` |
| 適用後BIN SHA-256 | `13205f1af92871a841cc8d41e25623e539cc48d890431451a1f254ca78eadeef` |
| 適用後BIN CRC32 | `0b3143c7` |

このBPSは対応する**元のRev 6 raw BIN**に単独で適用してください。下記の従来版を先に適用したBINへ重ねるものではありません。

### 従来版（塔・起動メニューのみ）

[`patches/Last Armageddon (Japan) (Rev 6) [reload + continue default].bps`](<patches/Last Armageddon (Japan) (Rev 6) [reload + continue default].bps>)

| 項目 | 値 |
|---|---|
| BPSサイズ | `624` bytes |
| BPS SHA-256 | `947f09c80aeecbb57a57f67e4aeb9a0f8db5af595a16a116582f0c0748e2a495` |
| 適用後BIN SHA-256 | `8b67cc40124a34eb6f238a87db323f6907fdeedda4bfed70d38ea6318e8f9d72` |
| 適用後BIN CRC32 | `411dcc8e` |

## 適用方法

[Floating IPS](https://github.com/Alcaro/Flips)など、BPS対応パッチャーを使用します。

```text
flips --apply --exact patch.bps original.bin patched.bin
```

GUI版Floating IPSでは「Apply Patch」を選択し、BPSファイル、対応する元BIN、出力先の順に指定します。

適用後のraw BINサイズはどちらも元と同じ`483310128` bytesです。使用した版に応じて、上記の適用後SHA-256を確認してください。

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

- 統合版BPSを対応元BINへ再適用し、生成物がv10修正版BINのSHA-256と完全一致することを確認済み。
- v10修正版BINから作成したCHDで、`chdman verify` のRaw SHA-1／Overall SHA-1検証に成功。
- v10 CHDを再展開したBINが、BPSの適用結果とSHA-256で完全一致することを確認済み。
- 元イメージとの差分は8つのMode 1物理セクタのみ。変更セクタすべてのEDC/P/Q ECC整合を確認済み。

詳細は[技術資料](docs/TECHNICAL.md)を参照してください。

## 注意事項

- 非公式パッチです。原作者、発売元、権利者、ハードウェア・エミュレータ開発者とは関係ありません。
- 元のイメージとセーブデータは必ずバックアップしてください。
- パッチ、パッチャー、変換ツールの使用は自己責任で行ってください。
