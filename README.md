## 　　Lesson34.グーグルサジェストを使う
#### 開発メモ
ワークフロー
<br><img width="600" src="https://user-images.githubusercontent.com/40127279/153736515-857e3014-bac3-4641-8646-afc05e532dcc.png">

### 1.ワークフローの構成
　Alfredワークフローサンプルでお馴染みのWEBツールです　
<br>　キーワードに関連する検索候補を挙げてくれるものです　
<br>　Google検索ボックスでも使えるので、わざわざワークフローにすることもないですが・・
<br>　フロー構成は単純でScriptFilterとWebSearchです
<br>　なお、今回のScriptFilterの出力はURLではなく検索ワードとなるので
<br>　OpenURLでなくWebSearchを使っています
### 2.URLアクセス
　キーワードは『グーグル』としています
<br>　ちょっと長いです。検索ワードが日本語となることを想定して、
<br>　キーワードを日本語としていますが、1文字キーワードにするのも良いですし、
<br>　Lesson4のようにバイリンガル起動にしてもよいでしょう
<br>　グーグルサジェストのURLは以下のもの
<br>　「q=」の後にキーワードをつけますがargの日本後をそのまま記述してもOKでした
`https://suggestqueries.google.com/complete/search?output=toolbar&q=`
<br>　
<br>　と、ここまでは良かったのですが実際に動かしてみるとちょっと想定外なことが起きました
<br>　一つは、リターンされるXMLのデータが文字コードでリターンされること
<br>　もう一つは、リターンされるワードが複数あるときに半角ブランクがはいること
<br>　
<br>　たとえば「オミクロン」とすると、ブラウザではこう返ってくるのですが
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/153735959-2f68825b-c000-424e-a5e2-87035dec580d.png">

<br>　
<br>　実際にCURLでリターンされているのは下記となります
```
<?xml version="1.0"?>
  <toplevel>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;&#x682A;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3; &#x6F5C;&#x4F0F;&#x671F;&#x9593;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;&#x3068;&#x306F;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;&#x682A;&#x3068;&#x306F;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;&#x682A; &#x7A7A;&#x6C17;&#x611F;&#x67D3;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;&#x306E;&#x6B21;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;&#x5F8C;&#x907A;&#x75C7;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3; &#x610F;&#x5473;"/>
    </CompleteSuggestion>
    <CompleteSuggestion>
      <suggestion data="&#x30AA;&#x30DF;&#x30AF;&#x30ED;&#x30F3;&#x611F;&#x67D3;&#x671F;&#x9593;"/>
     </CompleteSuggestion>
  </toplevel>
```
### 3.文字コードを文字に変換する
　XMLのdata=の部分がサジェストされるキーワードですが書式が1文字ずつ
<br>　「&#xnnnn;」となっています
<br>　調べてみるとUTF16BEのようですが、実は対処は簡単で下記のnkfのオプションでokです
```
nkf --numchar-input
```
### 4.半角ブランクへの対応
　半角ブランクはスクリプト上、重要な役割を果たしています
<br>　XML解析のgrepで抽出の際の区切り文字となり、そのまま配列に代入しています
<br>　もしXMLのリターンに半角ブランクがあると、区切り文字として捉えてしまい
<br>　配列が想定外に増加してしまいます
<br>　そこで下記のように半角ブランクを全角ブランクに変換して対応しました
```
sed  's/ /　/g'
```
### 5.JSONフォーマットの作成
<br>　XMLのリターンの一番先頭は、もとの検索ワードのそのままなので
<br>　2つ目からを表示させています
<br>　titleとargにサジェストされる文字をセットしてできあがりです
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/153735977-0cf00857-bc55-46f9-b3b4-1a8f10fdb4cc.png">


#### 取扱説明
### 機能:
　グーグルサジェストをAlfred出力フォーマットで表示する
### インストール:
　1.[alfredworkflow](https://github.com/KitanoTamotsu/googlesuggesut/releases/download/v1.0/googlesuggest.alfredworkflow.zip)をダウンロード 
<br>　2.ファイルをダブルクリックしてワークフローに登録

### 使い方:
　Alfredからキーワード『グーグル』で起動。続けて検索したい語句を入力する
<br>
<br>
[トップページに戻る](https://kitanotamotsu.github.io/)
