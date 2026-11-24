# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：第1回は素のLLMが自身の知識で答えたが、エージェントは回答前に search_glossary で用語集を確認してから答えるため、「3レベル」の内容が用語集の定義どおり正確だった（基礎・実装・発展の3段階で、どれか1つに取り組めば完了）。また、回答の最後に「関係する回：第1回」が示された。
- `--trace` で見えたツール呼び出し（ツール名と引数）：search_glossary({'query': 'LoRA'})、search_glossary({'query': 'QLoRA'})。用語ごとに1回ずつ、計2回呼ばれた。
- 1つの質問での LLM 呼び出し回数（`requests`）：2回
- 追加したツールと、docstring に書いた説明：make_quiz。指定した回の用語から確認問題を1問作って返すツールで、docstring には「確認問題やクイズを求められたときに使う」ことと、問題と答え（用語の定義）をJSONで返すことを書いた。登録しただけではモデルが get_lecture_summary で要点を読んで自作してしまい make_quiz が呼ばれなかったため、INSTRUCTIONS に「確認問題やクイズを求められたら make_quiz を使う」を追加したところ、make_quiz({'lecture': 2}) が呼ばれるようになり、「Hugging Face Hub」の確認問題が出題された。pytest は全件合格した。

## 宿題

- 取り組んだレベル：基礎
- やったこと：app/tools.py の search_glossary の docstring を「query には調べたい用語を1つだけ入れる」から「query には調べたい用語をまとめて入れる」に書き換え、同じ質問「LoRAとQLoRAの違いは？」を --trace 付きで実行して変更前後を比べた。
- 結果：変更前は search_glossary が用語ごとに2回呼ばれた（{'query': 'LoRA'}、{'query': 'QLoRA'}、tool_calls=2）が、変更後は {'query': 'LoRA QLoRA'} と1回にまとめて呼ばれるようになった（tool_calls=1、requests は2回で変わらず）。1回の検索でも部分一致で両方の用語がヒットし、回答の内容は変わらず正しかった。
- 気づき・疑問：コードを変えなくても docstring の一文を変えるだけでモデルのツール呼び出し方が変わることを確認できた。一方で make_quiz のように docstring だけでは呼ばれず INSTRUCTIONS への明記が必要な場合もあり、指示の置き場所によって効き方が違うと感じた。
