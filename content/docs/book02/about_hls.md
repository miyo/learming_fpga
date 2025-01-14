---
title: "高位合成とは？"
date: 2019-10-30T19:15:46+09:00
type: docs
draft: false
weight: -1800
---

FPGAを使うためには，長い間，VHDLあるいはVerilog HDLによるRTL設計が主流でした．しかし，HDLによるRTL設計は煩雑であり時間のかかる作業で簡単ではありません．そこで，最近では，高位合成という，HDLよりも高い水準での開発を可能する技術に注目が集まっています．特に，C/C++を利用したFPGA開発手法は，FPGAメーカーであるXilinxとIntelの両方から無償でツールが提供され，簡単にはじめられるようになりました．

この章では，実際に高位合成を使ってみる前に，高位合成とは何か，ということについて学んでおきましょう．

## FPGAとは - おさらい

Field Programmable Gate Array(FPGA)は，プログラム可能なハードウェアデバイスであり，ユーザが自由にハードウェアロジックをその上に構築できます．そのため，ASIC開発のプロトタイプ環境としての利用に加え，アプリケーションに応じたユーザ独自の専用ハードウェア開発の環境としても利用されます．

FPGAを用いたアプリケーション開発では，プロセッサ上でソフトウェアとして実装する場合に比べ，並列性の活用による低消費電力で高い処理能力の実現が期待される．アーキテクチャを工夫し，データ並列性とパイプライン並列性を活用することで，プロセッサと比較して数十倍から数百倍以上の性能向上が得られます．

また，単に高速に演算処理を実現できるというだけではなく，FPGAはアプリケーションを構成する処理回路が入出力信号を直接操作することができるため，低レイテンシ，高スループットで物理デバイスにアクセスできる点に強みを持ちます．加えて，クロックレベルで決定的な処理を実装できることも，アプリケーションを専用ハードウェアとして実装するときの強みです．
現代的なプロセッサで動作するソフトウェアでは，キャッシュなどの実行支援ユニットの動作や，複数のプログラムのコンテクスト切り替えなどによって，実行タイミングや処理にかかる時間を正確に管理することが難しくなっています．
一方で，専用ハードウェアとしてアプリケーションを実装する場合には，クロック単位での信号の変化を自分で制御することができます．

## FPGAを使う...のは辛い??

FPGAの性能を効率良く活用するためには，一般に，VHDLやVerilogを用いたRTL設計が必要で，プロセッサ上で動作するソフトウェア開発に比べて，人的，時間的な開発コストが大きいのが問題でした．特に，アルゴリズムとして複雑な処理のRTL記述は繁雑で手間がかかり，時にはバグの温床となっています．
また，アプリケーションに合ったアーキテクチャで処理を実装することができれば高い演算性能を達成することができる一方で，そうでない場合には処理性能でプロセッサやASICを凌駕することは困難です．
そのため，FPGAで高性能処理を実現するためにアーキテクチャ上の試行錯誤をする必要があり，このこともまた，開発コストを大きくする要因です．

## 高位合成の登場

ソフトウェア開発のように，手軽にFPGA開発を行えるようにする取り組みとして，プログラムをハードウェアロジックに変換する技術が高位合成です．特に，C/C++で記述されたプログラムをハードウェアロジックに変換する高位合成処理系は，多数開発，販売されています．従来，実用に足るツールは高価だったのですが，2018年現在では，FPGAメーカーであるXilinxとIntelの両方からVivado HLSおよびIntel HLSコンパイラというツールが，無償で提供されるようになり導入のコストがぐっと小さくなりました．

自由にC/C++の記述能力を利用することはできないものの，C/C++のループや条件分岐などの制御構文や，処理を関数にまとめてそれを呼び出す処理分割といったメリットを享受することができます．信号処理をソフトウェアで実装した場合にありがちな多重のループ文に対してもパイプライン化やアンロールでハードウェアの特徴を活用できるようになったこと，ターゲット周波数によって実装方式の探索をさせられることなどから，場合によっては手でがんばってハードウェアを設計するよりも，よい回路を生成できることもあるようです．

## C/C++ベースのFPGA開発のながれ
C/C++ベースのFPGAの開発，特にXilinxのVivado HLSを利用する流れを下の図に示します．要は，C/C++で記述したソースコードをVivado HLSによってVHDLまたはVerilog HDLに変換し，その変換後のコードをVivadoでFPGA用のビットストリームにする，というのが開発のおおまかな流れです．

{{<figure src="../about_hls_figures/hls_dev_flow.png" class="center" caption="Vivado HLSを使った開発フロー">}}

C/C++ベースで作ったモジュールを設計に組み込むためには，ツールへの慣れは必要になりますが，特段難しいものではありません．次章以降，簡単なサンプルでステップを踏んでみていきましょう．
