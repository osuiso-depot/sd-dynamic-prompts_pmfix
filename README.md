# fork版: Stable Diffusion Dynamic Prompts extension
* このバージョンでは、Stable Diffusion Dynamic Prompts extension使用時に「Prompt matrix」でエラーが出ないように修正しました。

## Dynamic Prompts and Random Seeds

Random seeds play an important role in controlling the randomness of the generated outputs. Let's discuss how Dynamic Prompts works with random seeds in different scenarios.

**重要な変更点:** 以前のバージョンで存在した `num_images_per_template` オプションは削除されました。現在、各プロンプトテンプレートからは常に1つの画像が生成されます。また、内部的に組み合わせ生成 (`is_combinatorial`) は無効化され、バッチ数 (`combinatorial_batches`) は1に固定されています。

### Without Dynamic Prompts Enabled

1.  **シードが -1 に設定されている場合:** ランダムなシードが選択されます。このシードは最初の画像の生成に使用され、次の画像はシード + 1 を使用して作成され、このパターンが後続の画像でも続きます。
2.  **シードが -1 より大きい特定の数値に設定されている場合:** 上記と同様のプロセスですが、ユーザーが指定したシードから開始されます。
3.  **バリエーションシードが定義されているが、バリエーション強度 (variation strength) がゼロの場合:** 上記の2点と同様のプロセスが維持されます。
4.  **バリエーションシードが 0 より大きい数値に設定されている場合:** すべての画像は同じ初期シード (ランダムに選択された、またはユーザーが設定したシード) を使用して生成されます。バリエーションシードはランダム ( -1 に設定されている場合) またはユーザーが選択した値です。最初の画像はバリエーションシードで生成され、次はバリエーションシード + 1 で生成されます。

### Using With Dynamic Prompts Enabled in Random/Standard Mode:

1.  **シードが -1 に設定されている場合:** 前のセクションの最初の点と同様のプロセスです。ただし、プロンプトも同じシードを使用して選択されます (ランダムプロンプトジェネレーターが使用される場合)。
2.  **シードが -1 より大きい数値に設定されている場合:** 前のセクションの2番目の点と同様のプロセスです。ただし、プロンプトジェネレーターが使用される場合、選択されたシードを使用してランダムなプロンプトも生成される点が異なります。
3.  **固定シード (fixed seed) チェックボックスがオンの場合:** すべての画像とプロンプトに同じシードが使用されます。これは、同じ画像が繰り返し生成されることを意味します (これは組み合わせ生成に役立ちます)。
4.  **固定シード (fixed seed) とプロンプトからシードをリンク解除 (unlink seed from prompt) の両方のチェックボックスがオンの場合:** プロンプトにはランダムなシードが使用されますが、すべての画像には同じシードが使用されます。この設定は、異なるプロンプトが同じ画像の生成にどのように影響するかを確認したい場合に役立ちます。

### Variation Seeds with Dynamic Prompts

1.  **バリエーション強度 (Variation strength) が 0 に設定されている場合:** バリエーションは無視されます。
2.  **バリエーション強度 (Variation strength) が 0 より大きい数値に設定されている場合:** 各画像にバリエーションシードが割り当てられ、毎回1ずつ増加します。ただし、同じ画像のバリエーションを探しているため、生成されるプロンプトは1つだけです。

### Combinatorial Mode with Variation Strength > 0

この場合、最初の画像のみが生成されます。これはおそらく意図した結果ではないため、設定を調整するか、別のモードを使用する必要があるかもしれません。
