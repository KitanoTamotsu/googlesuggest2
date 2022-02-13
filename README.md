## 　　Lesson34.グーグルサジェストを使う
#### 開発メモ
### 1.ワークフローの構成
　Alfredワークフローサンプルでお馴染みのWEBツールです　
<br>　キーワードに関連する検索候補を挙げてくれるものです　
<br>　Google検索ボックスでも使えるので、わざわざワークフローにすることもないですが・・
<br>　フロー構成は単純でScriptFilterとWebSearchです
<br>　なお、今回のScriptFilterの出力はURLではなく検索ワードとなるので
<br>　OpenURLでなくWebSearchを使っています
### 2.URLアクセス
　キーワードは『グーグル』としています
<br>　ちょっと長いです。検索ワードが日本語となることを想定して、キーワードを日本語としていますが
<br>　1文字キーワードにするのも良いですし、
<br>　Lesson4のようにバイリンガル起動にしてもよいでしょう
<br>　グーグルサジェストのURLは以下のもの
<br>　「q=」の後にキーワードをつけますがargの日本後をそのまま記述してもOKでした
`https://suggestqueries.google.com/complete/search?output=toolbar&q=`
<br>　と、ここまでは良かったのですが実際に動かしてみるとちょっと想定外なことが起きました
<br>　一つは、リターンされるXMLのデータが文字コードでリターンされること
<br>　もう一つは、リターンされるワードが複数あるときに半角ブランクがはいること
<br>　
<br>　たとえば「オミクロン」とすると、ブラウザではこう返ってくるのですが
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
　XMLのdata=の部分がサジェストされるキーワードですが書式が1文字ずつ「&#xnnnn;」となっています
<br>　調べてみるとUTF16BEのようですが、実は対処は簡単で下記のnkfのオプションでokです
<br>　｀nkf --numchar-input`
### 4.半角ブランクへの対応
　半角ブランクはスクリプト上、重要な役割を果たしています
<br>　XML解析のgrepで抽出の際の区切り文字となり、そのまま配列に代入しています
<br>　もしXMLのリターンに半角ブランクがあると、区切り文字として捉えてしまい
<br>　配列が想定外に増加してしまいます
<br>　そこで下記のように半角ブランクを全角ブランクに変換して対応しました
<br>　｀sed  's/ /　/g'｀
### 5.JSONフォーマットの作成
<br>　XMLのリターンの一番先頭は、もとの検索ワードのそのままなので2つ目からを表示させています
<br>　titleとargにサジェストされる文字をセットしてできあがりです
#### 取扱説明
### 機能:
　グーグルサジェストをAlfred出力フォーマットで表示する
### インストール:
　1.[alfredworkflow](https://github.com/KitanoTamotsu/noet/releases/download/1.0/googlesuggest.alfredworkflow.zip)をダウンロード 
<br>　2.ファイルをダブルクリックしてワークフローに登録

### 使い方:
　Alfredからキーワード『グーグル』で起動。続けて検索したい語句を入力する
<br>
<br>
[トップページに戻る](https://kitanotamotsu.github.io/)
