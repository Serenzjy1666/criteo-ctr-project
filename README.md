# Criteo 广告点击率预估

比较 13 个数值字段、额外 26 个类别字段及 FM 二阶交互的增量收益。用 Colab 打开 ctr_experiment.ipynb，从头运行全部单元格。首次运行需要访问 Hugging Face，数据和结果写入当前目录。


CriteoClickLogs 固定版本 a1a472783e135c6786617567f51bb64bfc4b031a，按原顺序取 day_0.gz 前 100,000 行。40 列为 label、I1–I13、C1–C26，共 2,684 次点击。早期 200 条练习数据与正式结果分开。
两次 train_test_split 都按当次输入的 label 分层，random_state=42。训练 70,000 条/1,879 次点击，验证 15,000 条/403 次点击，测试 15,000 条/402 次点击。测试集留到方案锁定后评估。


## 实验结论

在 13 个数值特征的基础上，模型 B 为 26 个类别字段加入独立的线性权重。相较仅使用数值特征的模型 A，B 的测试 AUC 提高 0.034804，Log Loss 降低 0.000605。


模型 B 的验证 AUC 比模型 C 高 0.000931，两者的验证 Log Loss 几乎持平。据此锁定第 5 轮的模型 B，随后在留出的测试集上得到 AUC 0.690224，Log Loss 0.121110。


在本次 10 万条样本、固定划分和训练配置下，加入 FM 二阶交互后，验证和测试 AUC 分别下降 0.000931 和 0.000542，两个集合上的 Log Loss 均几乎持平。本次实验未观察到 FM 相对线性模型 B 的稳定额外收益。

数据仅覆盖 day_0 开头的连续片段，使用片段内部随机分层划分。泛化到后续日期的表现需要单独验证。

Full-precision metrics from the local reproduction are in experiment_results.csv.
