# 使用 Keras 模型的文本摘要

> 原文：<https://medium.com/hackernoon/text-summarization-using-keras-models-366b002408d9>

![](img/4583707d1155706ea868e78cfb0a6e5b.png)

了解如何总结 Rajdeep Dua 和 Manpreet Singh Ghotra 撰写的本文，Raj deep Dua 目前领导 Salesforce India 的开发人员关系团队，Manpreet Singh gho tra 目前在 Salesforce 开发机器学习平台/API。

文本摘要是自然语言处理(NLP)中的一种方法，用于生成参考文档的简短而精确的摘要。手动生成大型文档的摘要是一项非常困难的任务。使用机器学习技术的文本摘要仍然是一个活跃的研究课题。在讨论文本摘要以及如何进行之前，这里有一个摘要的定义。

摘要是从一个或多个文本生成的文本输出，以更短的形式传达来自原始文本的相关信息。自动文本摘要的目标是使用语义将源文本转换成较短的版本。

最近，已经开发了各种方法用于使用 NLP 技术的自动文本摘要，并且它们已经在各种领域中广泛实现。一些例子包括搜索引擎创建用于文档预览的摘要，以及新闻网站产生新闻主题的综合描述，通常作为标题，以帮助用户浏览。

为了有效地总结文本，深度学习模型需要能够理解文档，并识别和提取重要信息。这些方法极具挑战性且非常复杂，尤其是当文档长度增加时。

# 评论的文本摘要

本文将向您展示如何解决文本摘要问题，为世界上最大的电子商务平台亚马逊上销售的精美食品的产品评论创建相关摘要。评论包括产品和用户信息、评级和纯文本评论。它还包括所有其他亚马逊类别的评论。通过定义编码器-解码器**递归神经网络** ( **RNN** )架构，开发一个基本的字符级**序列对序列** ( **seq2seq** )模型。

本文中使用的数据集可以在 https://www.kaggle.com/snap/amazon-fine-food-reviews/[找到。您的数据集将包括以下内容:](https://www.kaggle.com/snap/amazon-fine-food-reviews/)

568，454 条评论

256，059 个用户

74，258 件产品

## 怎么做…

您将开发一个建模管道和编码器-解码器架构，试图为一组给定的评论创建相关的摘要。建模管道使用通过 Keras functional API 编写的 RNN 模型。管道还使用各种数据操作库。

编码器-解码器架构被用作构建用于序列预测的 rnn 的一种方式。它包括两个主要部分:编码器和解码器。编码器读取完整的输入序列，并将其编码为内部表示，通常是固定长度的向量，称为上下文向量。另一方面，解码器从编码器读取编码的输入序列，并生成输出序列。可以使用各种类型的编码器，更常见的是使用双向 rnn，如 LSTMs。

## 数据处理

将正确的数据作为神经架构的输入，用于训练和验证，这一点至关重要。确保数据具有有用的比例和格式，并且包含有意义的要素。这将导致更好和更一致的结果。

采用以下工作流程进行数据预处理:

1.使用 pandas 加载数据集

2.将数据集分割成机器学习的输入和输出变量

3.对输入变量应用预处理转换

4.汇总数据以显示变化

现在一步一步开始:

1.从导入重要的包和数据集开始。使用`pandas`库加载数据并检查数据集的形状——它包括 10 个特征和 500 万个数据点:

```
import pandas as pdimport refrom nltk.corpus import stopwordsfrom pickle import dump, loadreviews = pd.read_csv("/deeplearning-keras/ch09/summarization/Reviews.csv")print(reviews.shape)print(reviews.head())print(reviews.isnull().sum())
```

输出如下所示:

```
(568454, 10)Id 0ProductId 0UserId 0ProfileName 16HelpfulnessNumerator 0HelpfulnessDenominator 0Score 0Time 0Summary 27Text 0
```

2.删除空值和不需要的功能，如以下代码片段所示:

```
reviews = reviews.dropna()reviews = reviews.drop(['Id','ProductId','UserId','ProfileName','HelpfulnessNumerator','HelpfulnessDenominator', 'Score','Time'], 1)reviews = reviews.reset_index(drop=True) print(reviews.head())for i in range(5):print("Review #",i+1)print(reviews.Summary[i])print(reviews.Text[i])print()
```

输出如下所示:

```
**Summary Text**0 Good Quality Dog Food I have bought several of the Vitality canned d...1 Not as Advertised Product arrived labeled as Jumbo Salted Peanut...2 "Delight," says it all This is a confection that has been around a fe...3 Cough Medicine If you are looking for the secret ingredient i...**Review # 1**Not as Advertised - Product arrived labeled as Jumbo Salted Peanuts...the peanuts were actually small sized unsalted. Not sure if this was an error or if the vendor intended to represent the product as "Jumbo".**Review # 2**"Delight" says it all - This is a confection that has been around a few centuries. It is a light, pillowy citrus gelatin with nuts - in this case, Filberts. And it is cut into tiny squares and then liberally coated with powdered sugar. And it is a tiny mouthful of heaven. Not too chewy, and very flavorful. I highly recommend this yummy treat. If you are familiar with the story of C.S. Lewis' "The Lion, The Witch, and The Wardrobe" - this is the treat that seduces Edmund into selling out his Brother and Sisters to the Witch.**Review # 3**Cough Medicine - If you are looking for the secret ingredient in Robitussin I believe I have found it. I got this in addition to the Root Beer Extract I ordered (which was good) and made some cherry soda. The flavor is very medicinal.
```

根据定义，缩写是将两个单词组合成简化形式，省略一些内部字母并使用撇号。可以从[http://stack overflow . com/questions/1979 01 88/expanding-English-language-contractions-in-python](http://stackoverflow.com/questions/19790188/expanding-english-language-contractions-in-python)获取`contractions`的列表。

3.将缩写替换为更长的形式，如下所示:

```
**contractions** = {"ain't": "am not","aren't": "are not","can't": "cannot","can't've": "cannot have","'cause": "because","could've": "could have","couldn't": "could not","couldn't've": "could not have","didn't": "did not","doesn't": "does not","don't": "do not","hadn't": "had not","hadn't've": "had not have","hasn't": "has not","haven't": "have not","he'd": "he would","he'd've": "he would have",
```

4.通过替换缩写和删除停用词来清理文本文档:

```
def **clean_text**(text, remove_stopwords=True):# Convert words to lower casetext = text.lower()if True:text = text.split()new_text = []for word in text:if word in contractions:new_text.append(contractions[word])else:new_text.append(word)text = " ".join(new_text)text = re.sub(r'https?:\/\/.*[\r\n]*', '', text, flags=re.MULTILINE)text = re.sub(r'\<a href', ' ', text)text = re.sub(r'&amp;', '', text)text = re.sub(r'[_"\-;%()|+&=*%.,!?:#$@\[\]/]', ' ', text)text = re.sub(r'<br />', ' ', text)text = re.sub(r'\'', ' ', text)if remove_stopwords:text = text.split()stops = set(stopwords.words("english"))text = [w for w in text if not w in stops]text = " ".join(text)return text
```

5.删除不需要的字符，并可以选择停止单词。此外，确保更换`contractions`，如前所示。你可以从**自然语言工具包** ( **NLTK** )中获得停用词列表，这有助于从段落中拆分句子、拆分单词以及识别词性。使用以下命令导入工具包:

```
import nltknltk.download('stopwords')
```

6.清理摘要，如以下代码片段所示:

```
# Clean the summaries and textsclean_summaries = []for summary in reviews.Summary:clean_summaries.append(clean_text(summary, remove_stopwords=False))print("Summaries are complete.")clean_texts = []for text in reviews.Text:clean_texts.append(clean_text(text))print("Texts are complete.")
```

7.最后，将所有评论保存到一个`pickle`文件中。`pickle`序列化对象，使其可以保存到文件中，并在以后再次加载到程序中:

```
stories = list()for i, text in enumerate(clean_texts):stories.append({'story': text, 'highlights': clean_summaries[i]})# save to filedump(stories, open('/deeplearning-keras/ch09/summarization/review_dataset.pkl', 'wb'))
```

## 编码器-解码器架构

为文本摘要开发一个基本的字符级 seq2seq 模型。使用单词级模型，这在文本处理领域很常见。对于本文，使用角色级别模型。如前所述，编码器和解码器架构是为序列预测创建 rnn 的一种方式。编码器读取整个输入序列，并将其编码成一个内部表示，通常是一个名为上下文向量的固定长度向量。另一方面，解码器从编码器读取编码的输入序列，并产生输出序列。

编码器-解码器架构包括两个主要模型:一个读取输入序列并将其编码为固定长度的向量，另一个解码固定长度的向量并输出预测序列。这个架构是为 seq2seq 问题设计的。

1.首先，定义超参数，例如批量大小、用于训练的时期数以及用于训练的样本数:

```
batch_size = 64epochs = 110latent_dim = 256num_samples = 10000
```

2.接下来，从`pickle`文件加载`review`数据集:

```
stories = load(open('/deeplearning-keras/ch09/summarization/review_dataset.pkl', 'rb'))print('Loaded Stories %d' % len(stories))print(type(stories))
```

输出如下所示:

```
Loaded Stories **568411**
```

3.然后，对数据进行矢量化:

```
input_texts = []target_texts = []input_characters = set()target_characters = set()for story in stories:input_text = story['story']for highlight in story['highlights']:target_text = highlight# We use "tab" as the "start sequence" character# for the targets, and "\n" as "end sequence" character.target_text = '\t' + target_text + '\n'input_texts.append(input_text)target_texts.append(target_text)for char in input_text:if char not in input_characters:input_characters.add(char)for char in target_text:if char not in target_characters:target_characters.add(char)input_characters = sorted(list(input_characters))target_characters = sorted(list(target_characters))num_encoder_tokens = len(input_characters)num_decoder_tokens = len(target_characters)max_encoder_seq_length = max([len(txt) for txt in input_texts])max_decoder_seq_length = max([len(txt) for txt in target_texts])print('Number of samples:', len(input_texts))print('Number of unique input tokens:', num_encoder_tokens)print('Number of unique output tokens:', num_decoder_tokens)print('Max sequence length for inputs:', max_encoder_seq_length)print('Max sequence length for outputs:', max_decoder_seq_length)
```

输出如下所示:

```
Number of samples: 568411Number of unique input tokens: 84Number of unique output tokens: 48Max sequence length for inputs: 15074Max sequence length for outputs: 5
```

4.现在，创建一个通用函数来定义编码器-解码器 RNN:

```
def **define_models**(n_input, n_output, n_units):# define training encoderencoder_inputs = Input(shape=(None, n_input))encoder = LSTM(n_units, return_state=True)encoder_outputs, state_h, state_c = encoder(encoder_inputs)encoder_states = [state_h, state_c]# define training decoderdecoder_inputs = Input(shape=(None, n_output))decoder_lstm = LSTM(n_units, return_sequences=True, return_state=True)decoder_outputs, _, _ = decoder_lstm(decoder_inputs, initial_state=encoder_states)decoder_dense = Dense(n_output, activation='softmax')decoder_outputs = decoder_dense(decoder_outputs)model = Model([encoder_inputs, decoder_inputs], decoder_outputs)# define inference encoderencoder_model = Model(encoder_inputs, encoder_states)# define inference decoderdecoder_state_input_h = Input(shape=(n_units,))decoder_state_input_c = Input(shape=(n_units,))decoder_states_inputs = [decoder_state_input_h, decoder_state_input_c]decoder_outputs, state_h, state_c = decoder_lstm(decoder_inputs,  initial_state=decoder_states_inputs)decoder_states = [state_h, state_c]decoder_outputs = decoder_dense(decoder_outputs)decoder_model = Model([decoder_inputs] + decoder_states_inputs, [decoder_outputs] + decoder_states)# return all modelsreturn model, encoder_model, decoder_model
```

## 培养

1.运行训练时，使用`rmsprop`优化器和`categorical_crossentropy`作为`loss`功能:

```
# Run trainingmodel.compile(optimizer='rmsprop', loss='categorical_crossentropy')model.fit([encoder_input_data, decoder_input_data], decoder_target_data,batch_size=batch_size,epochs=epochs,validation_split=0.2)# Save modelmodel.save('/deeplearning-keras/ch09/summarization/model2.h5')
```

输出如下所示:

```
64/800 [=>............................] - ETA: 22:05 - loss: 2.1460128/800 [===>..........................] - ETA: 18:51 - loss: 2.1234192/800 [======>.......................] - ETA: 16:36 - loss: 2.0878256/800 [========>.....................] - ETA: 14:38 - loss: 2.1215320/800 [===========>..................] - ETA: 12:47 - loss: 1.9832384/800 [=============>................] - ETA: 11:01 - loss: 1.8665448/800 [===============>..............] - ETA: 9:17 - loss: 1.7547512/800 [==================>...........] - ETA: 7:35 - loss: 1.6619576/800 [====================>.........] - ETA: 5:53 - loss: 1.5820512/800 [==================>...........] - ETA: 7:19 - loss: 0.7519576/800 [====================>.........] - ETA: 5:42 - loss: 0.7493640/800 [=======================>......] - ETA: 4:06 - loss: 0.7528704/800 [=========================>....] - ETA: 2:28 - loss: 0.7553768/800 [===========================>..] - ETA: 50s - loss: 0.7554
```

2.要进行推断，请使用以下方法:

```
# generate target given source sequencedef predict_sequence(infenc, infdec, source, n_steps, cardinality):# encodestate = infenc.predict(source)# start of sequence inputtarget_seq = array([0.0 for _ in range(cardinality)]).reshape(1, 1, cardinality)# collect predictionsoutput = list()for t in range(n_steps):# predict next charyhat, h, c = infdec.predict([target_seq] + state)# store predictionoutput.append(yhat[0,0,:])# update statestate = [h, c]# update target sequencetarget_seq = yhatreturn array(output)
```

输出如下所示:

```
Review(1): The coffee tasted great and was at such a good price! I highly recommend this to everyone!Summary(1): great coffeeReview(2): This is the worst cheese that I have ever bought! I will never buy it again and I hope you won't either!Summary(2): omg gross grossReview(3): love individual oatmeal cups found years ago sam quit selling sound big lots quit selling found target expensive buy individually trilled get entire case time go anywhere need water microwave spoon to know quaker flavor packetsSummary(3): love it
```

*如果你觉得这篇文章很有趣，你可以探索一下* [*Keras 深度学习食谱*](https://www.amazon.com/Keras-Deep-Learning-Cookbook-implementing/dp/1788621751) *利用深度学习和 Keras 的力量来开发更智能、更高效的数据模型。* [*Keras 深度学习食谱*](https://www.packtpub.com/big-data-and-business-intelligence/keras-deep-learning-cookbook) *向您展示了如何在广受欢迎的 Keras 库的帮助下，解决训练高效深度学习模型时遇到的不同问题。*