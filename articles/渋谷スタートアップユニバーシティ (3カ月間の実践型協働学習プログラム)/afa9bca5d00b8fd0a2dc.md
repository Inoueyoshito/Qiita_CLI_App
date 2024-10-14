---
id: afa9bca5d00b8fd0a2dc
title: 渋谷スタートアップユニバーシティ (3カ月間の実践型協働学習プログラム)
created_at: 2024-10-15T00:07:40+09:00
updated_at: 2024-10-15T00:07:40+09:00
tags: [{"name":"qiita-cli","versions":[]}]
private: false
url: https://qiita.com/ora_yoshito/items/afa9bca5d00b8fd0a2dc
likes_count: 0
--- 
      
# 渋谷スタートアップユニバーシティについて
#### 渋谷区が主催する「渋谷スタートアップユニバーシティ」に参加し、社会人向けの3か月間の実践型協働学習プログラムに取り組みました。このプログラムは、国内企業を代表するCEOやCTOの講義を受けつつ、3人1組のチームで開発を行い、最終的にピッチコンテストで成果を発表するというものです。

# プロダクト開発 (概要)
#### 行政手続きをより簡単に、そして手軽にできるWebアプリケーションを開発しました。

# 開発背景
#### 現状の行政サービスでは、多くの手続きが紙ベースで行われており、ユーザーにとっても担当者にとっても多くの時間と労力がかかるという課題がありました。特に、書類の手書きや物理的な郵送が面倒で、効率化が望まれていました。
- 「申請書類の手続きが多い」
- 「全ての書類が手書き」
#### 上記の、２つの行政サービスにおける問題に課題を感じ、それを解決するプロダクトを開発しました。

# チームでの自身の役割
#### チーム開発では、デザイナー1、エンジニア2で分かれて、私は主にバックエンド開発を担当し、Pythonを用いて書類のテンプレートPDFと新たに生成したPDFページを合成する自動化プログラムを作成しました。

# 作成したプログラムのイメージ
#### アプリから(戸籍謄本,転居届...)の入力した情報を、空白のテンプレートに、情報を自動的に入力していきます。例えば、各項目（氏名や生年月日など）が指定された位置にぴったりと収まり、最終的にはテンプレートに書き込まれたPDFファイルが出来上がるというイメージです

# 住民票関係請求書.pdfの例
```import os
from PyPDF2 import PdfFileWriter, PdfFileReader
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import A4, portrait
from reportlab.lib.units import inch, mm, cm
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont

# ファイルの指定
template_file = './resource/住民票関係請求書.pdf' # 既存のテンプレートPDF
output_file = './output/住民票関係請求書_result.pdf' # 完成したPDFの保存先
tmp_file = './__tmp.pdf' # 一時ファイル

# A4縦のCanvasを作成 -- (*1)
w, h = portrait(A4)
cv = canvas.Canvas(tmp_file, pagesize=(w, h))

# フォントを登録しCanvasに設定 --- (*2)
font_size = 10
ttf_file = './ipaexm.ttf'
pdfmetrics.registerFont(TTFont('IPAexGothic', ttf_file))
cv.setFont('IPAexGothic', font_size)

# 文字列を描画する --- (*3)
cv.setFillColorRGB(0, 0, 0)
cv.drawString(55*mm, h-43*mm, "渋谷区　神宮前　１丁目１　１−１　１１１号室")
cv.drawString(40*mm, h-50*mm, "ツルタ ヒロム")
cv.drawString(40*mm, h-60*mm, "鶴田 啓")
cv.drawString(156*mm, h-53*mm, "1993")
cv.drawString(172*mm, h-53*mm, "7")
cv.drawString(182*mm, h-53*mm, "15")

# 一時ファイルに保存 --- (*4)
cv.showPage()
cv.save()

# テンプレートとなるPDFを読む --- (*5)
template_pdf = PdfFileReader(template_file)
template_page = template_pdf.getPage(0)

# 一時ファイルを読んで合成する --- (*6)
tmp_pdf = PdfFileReader(tmp_file)
template_page.mergePage(tmp_pdf.getPage(0))

# 書き込み先PDFを用意 --- (*7)
output = PdfFileWriter()
output.addPage(template_page)
with open(output_file, "wb") as fp:
  output.write(fp)

# 一時ファイル削除
os.remove(tmp_file)
```
① 必要なライブラリをインポートする。
② PDFテンプレートや出力ファイルのパスを指定する。
③ 新しいPDFを作成するためのキャンバスを用意する。
④ 日本語フォントを設定して使用できるようにする。
⑤ 文字列や情報を指定した位置に描画する。
⑥ 各項目（氏名、住所など）に対応するデータを指定位置に配置する。
⑦ 描画内容を一時PDFファイルとして保存する。
⑧ テンプレートPDFに描画済みのPDFを重ねる。
⑨ 完成したPDFを指定した場所に保存する。

# 頑張った点
#### このプロジェクトにおいて、頑張った点は、日本語フォントの使用やテンプレートPDFとの合成です。PDF生成に使用したライブラリはReportLabやPyPDF2で、これらを組み合わせて、行政の申請書類の自動生成を実現しました。また、PDFの合成処理では、一時ファイルを使用し、テンプレートに直接変更を加えることなく、動的に内容を追加できるように工夫しました。これにより、柔軟かつ効率的なPDF生成が可能になり、書類のカスタマイズも容易になりました。

# 工夫した点
#### 一時ファイルの管理には特に注意を払い、テンプレートファイルに変更を加えずに処理できるようにしたほか、不要なファイルが残らないように自動的にクリーンアップする仕組みを実装しました。このアプローチにより、サーバーのリソースを節約しつつ、システムの安定性を確保しました。

# チーム開発ので経験
#### 苦労した点は、自身と一緒に開発するチームとの認識の齟齬が発生しないようにすることです。具体的には、仕様に関する認識の違いから、プロジェクトの進行が遅れたことがありました。この問題を解決するために、多種多様なアイディアや考え方がある中で、お互いの認識を合わせることが難しいと感じました。

#### その中で工夫した点では、私は議論的なコミュニケーションを意識的に取ることにしました。実体験として、週に1,2回の開発ミーティングをして、進捗や課題について話し合う時間を確保しました。また、議論の際には意見が対立することもあるため、相手の意見を尊重しながらも、自分の考えを明確に伝えるよう心掛けました。この結果、チーム内のコミュニケーションが活発になり、お互いの意見や認識の解像度が深まることで、認識の齟齬が発生しにくくなりました。最終的には、プロジェクトを予定通りに進められ、ピッチコンテストの期限を守ることができました。

# 最後に
#### 開発したプロダクトは、手間のかかる申請手続きを効率化し、行政サービスをよりシンプルで「楽」にする一助を提供できるものとなりました。このプロジェクトを通じて、チームでの開発経験を深め、問題解決に向けたエンジニアリングスキルを高めることができました。
