#X2Vec整体认识
https://blog.csdn.net/sinat_26917383/article/details/69666596
https://github.com/MaxwellRebo/awesome-2vec 

#词向量 Word2Vec 也叫word embeddings 
  * 如何在巨大的词典中表示某个词
  * 用什么样的方式表示词，才能更方便的描述词与词之间的关系？

  参考：https://www.cnblogs.com/pinard/p/7160330.html  
   https://www.cnblogs.com/iloveai/p/gensim_tutorial2.html  
   
  Embedding其实是将词或者句子/文档向量化
  word2vec是google在2013年推出的一个NLP工具，它的特点是将所有的词向量化，这样词与词之间就可以定量的去度量他们之间的关系，挖掘词之间的联系。
  非常清楚的介绍了word2vec的前世今生: http://www.cnblogs.com/iloveai/p/word2vec.html    
  
##CBOW

##Skip-gram  

#Doc2Vec
https://www.cnblogs.com/iloveai/p/gensim_tutorial2.html

#FastText
http://www.52nlp.cn/fasttext
https://blog.csdn.net/sinat_26917383/article/details/54850933


#tensorflow word2vec
http://www.tensorfly.cn/tfdoc/tutorials/word2vec.html


# Notes
* 通常图像或音频系统处理的是由图片中所有单个原始像素点强度值或者音频中功率谱密度的强度值，把它们编码成丰富、高纬度的向量数据集。
对于物体或语音识别这一类的任务，我们所需的全部信息已经都存储在原始数据中（显然人类本身就是依赖原始数据进行日常的物体或语音识别的）。
  换言之，图像和语音实际上更为直接，是什么需要的信息依然表示在原始的数据特征里面了。而语言本身则更为复杂，
  自然语言处理系统通常将词汇作为离散的单一符号，例如 "cat" 一词或可表示为 Id537 ，而 "dog" 一词或可表示为 Id143。
  这些符号编码毫无规律，无法提供不同词汇之间可能存在的关联信息。换句话说，在处理关于 "dogs" 一词的信息时，模型将无法利用已知的关于 "cats" 的信息（例如，它们都是动物，有四条腿，可作为宠物等等）。
  可见，将词汇表达为上述的独立离散符号将进一步导致数据稀疏，使我们在训练统计模型时不得不寻求更多的数据。而词汇的向量表示将克服上述的难题。






    
    
    
    