# sentence_embedding

Google has built a model to produce sentence vectors given any English sentences.

[universal-sentence-encoder](https://github.com/arfu2016/nlp/tree/master/nlp_data/large/universal-sentence-encoder)

Google模型计算句向量，句向量效果的评估

[sentence_vector_evaluation](https://github.com/arfu2016/nlp/tree/master/nlp_models/sentence_vector_evaluation)

简单的评估一是可以看聚类，很多句子的情况下，意思相近的能不能聚在一起，相互之间相似度较大；二是看分类，最简单的就是k=1情况下的knn，有几个句子作为已有的带搜索的标准，给出新的句子，看它是不是和意思最为相近的句子相似度最大，也就是看分类是否正确。

用词向量用baseline方法计算句向量

[sentence_vector_aggregated](https://github.com/arfu2016/nlp/tree/master/nlp_models/sentence_vector_aggregated)

用成对句子相似度来测试句向量的效果: for english sentences

[sts-benchmark](https://github.com/arfu2016/nlp/tree/master/nlp_data/small/sts-benchmark)

用句向量实现句子分类

[sentence_similar_classification](https://github.com/arfu2016/nlp/tree/master/nlp_models/sentence_similar_classification)

自然语言处理的核心其实就是句向量问题，从易到难包括三个部分：句子相似度问题，句子分类（聚类）问题和文档问句问题。

在计算长文档的向量时有两种思路：一是长文档向量由词向量来算，二是长文档向量由短句向量来算。

前面提到的计算句向量的baseline方法是把组成句子的词向量用average pooling来得到句向量，根据code repository中的baseline_system.ipynb，除了average pooling，还可以用max pooling，average pooling的结果与max pooling的结果相连接，用n-gram model先average pooling再max pooling. 在[sentence_vector](https://github.com/arfu2016/nlp/tree/master/nlp_models/sentence_vector)中有实现。

上面是简单的计算句向量的方法，可以说是non-parametric的方法，不需要根据数据来拟合，除此之外，还有parametric的方法，一般是用deep learning的方法，用cnn或者lstm的方法。

cnn在[Text Classification with CNN and RNN](https://github.com/arfu2016/text-classification-cnn-rnn)中有实现，相比用n-gram model先average pooling再max pooling，多了不少待拟合的参数，多出来的参数个数是filter_size*word_embed_dimention*hidden_unit_number + hidden_unit_number，至少也是数以万计的参数。在得到cnn layer后，还要进行max pooling，得到单一向量，这一步和n-gram model中的max pooling没啥区别。

lstm在[text-classification](https://github.com/arfu2016/DuReader/tree/master/text-classification)中有实现，就是一般的lstm用rnn展开，值得注意的是，最后使用的句向量是最后一步的output，在上面的Text Classification with CNN and RNN中也是这样实现的。
