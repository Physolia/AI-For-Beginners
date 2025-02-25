# テキストをテンソルとして表現する

## [講義前のクイズ](https://red-field-0a6ddfd03.1.azurestaticapps.net/quiz/113)

## テキスト分類

このセクションの最初の部分では、**テキスト分類**タスクに焦点を当てます。私たちは、以下のようなニュース記事を含む[AG News](https://www.kaggle.com/amananandrai/ag-news-classification-dataset)データセットを使用します。

* カテゴリー: Sci/Tech
* タイトル: Ky. Company Wins Grant to Study Peptides (AP)
* 本文: AP - ケンタッキー大学の化学研究者によって設立された会社が、ペプチドを開発するための助成金を受けました...

私たちの目標は、テキストに基づいてニュース項目をカテゴリの一つに分類することです。

## テキストの表現

自然言語処理（NLP）タスクをニューラルネットワークで解決したい場合、テキストをテンソルとして表現する方法が必要です。コンピュータはすでに、ASCIIやUTF-8などのエンコーディングを使用して、テキスト文字を画面上のフォントにマッピングする数値として表現しています。

<img alt="文字をASCIIおよびバイナリ表現にマッピングする図を示す画像" src="images/ascii-character-map.png" width="50%"/>

> [画像の出典](https://www.seobility.net/en/wiki/ASCII)

人間として、私たちは各文字が**何を表しているか**を理解し、すべての文字がどのように組み合わさって文の単語を形成するかを理解しています。しかし、コンピュータはそれ自体ではそのような理解を持っておらず、ニューラルネットワークはトレーニング中に意味を学ぶ必要があります。

したがって、テキストを表現する際にはさまざまなアプローチを使用できます：

* **文字レベルの表現**：テキストを各文字を数値として扱うことで表現します。テキストコーパスに*C*の異なる文字がある場合、単語*Hello*は5x*C*のテンソルで表現されます。各文字はワンホットエンコーディングのテンソル列に対応します。
* **単語レベルの表現**：テキスト内のすべての単語の**語彙**を作成し、その後、ワンホットエンコーディングを使用して単語を表現します。このアプローチは、各文字自体にはあまり意味がないため、より高次の意味概念である単語を使用することでニューラルネットワークのタスクを簡素化するため、ある程度優れています。しかし、大きな辞書サイズを考慮すると、高次元のスパーステンソルに対処する必要があります。

表現に関係なく、最初にテキストを**トークン**のシーケンスに変換する必要があります。1つのトークンは文字、単語、または時には単語の一部です。その後、トークンを数値に変換し、通常は**語彙**を使用して、この数値をワンホットエンコーディングを介してニューラルネットワークに供給します。

## Nグラム

自然言語において、単語の正確な意味は文脈の中でのみ決定できます。たとえば、*neural network*と*fishing network*の意味はまったく異なります。この点を考慮する方法の一つは、単語のペアに基づいてモデルを構築し、単語のペアを別々の語彙トークンとして扱うことです。この方法では、文*I like to go fishing*は次のトークンのシーケンスで表現されます：*I like*, *like to*, *to go*, *go fishing*。このアプローチの問題は、辞書のサイズが大幅に増加し、*go fishing*や*go shopping*のような組み合わせが異なるトークンによって表現され、同じ動詞にもかかわらず意味的な類似性を共有しないことです。

場合によっては、三語の組み合わせであるトライグラムを使用することも考えられます。このように、このアプローチはしばしば**n-grams**と呼ばれます。また、文字レベルの表現でn-gramsを使用することも理にかなっており、その場合n-gramsは異なる音節に大まかに対応します。

## ボキャブラリとTF/IDF

テキスト分類のようなタスクを解決するためには、テキストを固定サイズのベクトルで表現できる必要があります。このベクトルを最終的な密な分類器への入力として使用します。これを行う最も簡単な方法の一つは、すべての個々の単語の表現を組み合わせること、つまり加算することです。各単語のワンホットエンコーディングを加えると、テキスト内で各単語が出現する回数を示す頻度ベクトルが得られます。このようなテキストの表現は**ボキャブラリ**（BoW）と呼ばれます。

<img src="images/bow.png" width="90%"/>

> 作者による画像

BoWは、テキスト内に出現する単語とその数量を表現しており、テキストの内容を示す良い指標となります。たとえば、政治に関するニュース記事は、*president*や*country*などの単語を含む可能性が高く、科学的な出版物には*collider*や*discovered*などの単語が含まれるでしょう。したがって、単語の頻度は多くの場合、テキストの内容を示す良い指標となります。

BoWの問題は、*and*や*is*などの特定の一般的な単語がほとんどのテキストに現れ、最高の頻度を持ち、実際に重要な単語を隠してしまうことです。これらの単語の重要性を低下させるために、単語が全体の文書コレクション内で出現する頻度を考慮に入れることができます。これがTF/IDFアプローチの主なアイデアであり、このレッスンに添付されたノートブックで詳しく説明されています。

しかし、これらのアプローチではテキストの**意味**を完全に考慮することはできません。これを行うためには、より強力なニューラルネットワークモデルが必要であり、これについてはこのセクションの後半で説明します。

## ✍️ 演習: テキスト表現

以下のノートブックで学習を続けてください：

* [PyTorchによるテキスト表現](../../../../../lessons/5-NLP/13-TextRep/TextRepresentationPyTorch.ipynb)
* [TensorFlowによるテキスト表現](../../../../../lessons/5-NLP/13-TextRep/TextRepresentationTF.ipynb)

## 結論

これまでのところ、異なる単語に頻度の重みを追加できる技術を学びました。しかし、これらは意味や順序を表現することができません。著名な言語学者J. R. Firthが1935年に言ったように、「単語の完全な意味は常に文脈に依存しており、文脈から意味を除いた研究は真剣に受け止められません。」コースの後半では、言語モデリングを使用してテキストから文脈情報を取得する方法を学びます。

## 🚀 チャレンジ

ボキャブラリや異なるデータモデルを使用して他の演習に挑戦してみてください。この[コンペティション](https://www.kaggle.com/competitions/word2vec-nlp-tutorial/overview/part-1-for-beginners-bag-of-words)がインスピレーションになるかもしれません。

## [講義後のクイズ](https://red-field-0a6ddfd03.1.azurestaticapps.net/quiz/213)

## レビューと自己学習

[Microsoft Learn](https://docs.microsoft.com/learn/modules/intro-natural-language-processing-pytorch/?WT.mc_id=academic-77998-cacaste)でテキスト埋め込みとボキャブラリ技術を練習しましょう。

## [課題: ノートブック](assignment.md)

**免責事項**：  
この文書は、機械翻訳AIサービスを使用して翻訳されています。正確性を追求していますが、自動翻訳には誤りや不正確さが含まれる可能性があります。元の文書はその母国語での権威ある情報源と見なされるべきです。重要な情報については、専門の人間による翻訳を推奨します。この翻訳の使用に起因する誤解や誤訳については、当社は責任を負いません。