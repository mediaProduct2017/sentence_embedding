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

sentence similarity问题可以用来评估句向量的优劣。

有一个英文的sentence similarity的[数据集](http://ixa2.si.ehu.es/stswiki/images/4/48/Stsbenchmark.tar.gz)，大概有上千个句子对，有人工的相似度打分。用算法得到这些句子对的相似度之后，可以和人工打分比较，求person correlation coefficient. 相关系数越大，可以表明计算句向量的算法越好。

google的universal-sentence-encoder算法google_tf_hub的相关系数大概是0.75的样子，很不错

但是，基于多任务深度神经网络训练出来的这个算法如果在cpu上跑的话，大概要接近4s的时间，对于在线服务是无法忍受的，所以还是有必要基于词向量开发新的句向量算法。

一般的思路是用cnn或者rnn从词向量得到句向量，但如果数据量比较少的话，这种方法有太多的参数需要拟合，效果不好，可以采用简单的词向量求平均、求最大、平均+最大、以及n-gram model。

词向量直接求平均或者求最大是最直接的想法，效果也差不多，比如，词向量直接求平均算法spacy_average，得到的相关系数不足0.6，大概是0.59.

利用词向量的n-gram model，内部求平均，外部求最大，因为n-gram类似cnn，理论上可以抓住词汇以外的词序信息，但因为是非参方法，没有拟合参数，所以效果不一定好。实际试验中，词向量的n-gram model算法spacy_ngram，得到的相关系数0.61，相比直接求平均有微小提升。

利用词向量的平均+最大方法，就是把平均法和最大法得到的向量连起来作为新的向量，抓不住词序信息，但效果还不错，算法spacy_concat得到的相关系数有大概0.7.

英文的语料大概有1000条，词汇不到一万，spacy中也可以直接导入拟合好的glove的词向量，先算出句向量在分类效果比较好，每个类别大概有3-10个句子。直接上深度模型的话，1000条语料还是少了点，效果不如前面的方法。

中文的语料大概2000条，因为分词器不是很理想，用的是未知的字向量，字的数目大概在一万多（字向量用的是64维），直接用cnn来拟合最简单的深度模型，参数在百万级别，效果还是比bag of words要好，但不如英文模型的用已知词向量先算句向量的方法。

未来，如果语料能更多，可以直接拟合深度学习模型，或者先自己用多任务的方法拟合句向量，salesfore有一篇decanlp的论文甚至开源了代码，可以用来拟合句向量（google的方法只开源了拟合出来的模型，但没有开源拟合模型的代码）。
