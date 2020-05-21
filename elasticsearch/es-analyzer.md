# es 分析器学习

## 定义
An analyzer  — whether built-in or custom — is just a package which contains three lower-level building blocks: character filters, tokenizers, and token filters.

The built-in analyzers pre-package these building blocks into analyzers suitable for different languages and types of text. Elasticsearch also exposes the individual building blocks so that they can be combined to define new custom analyzers.

分析器（无论是内置的还是自定义的）只是一个包，其中包含三个较低级别的构建块：字符过滤器，令牌生成器和令牌过滤器。内置的分析器将这些构件预先打包为适合不同语言和文本类型的分析器。 Elasticsearch还公开了各个构建基块，以便可以将其组合以定义新的自定义分析器。

## character filters

A character filter receives the original text as a stream of characters and can transform the stream by adding, removing, or changing characters. For instance, a character filter could be used to convert Hindu-Arabic numerals (٠‎١٢٣٤٥٦٧٨‎٩‎) into their Arabic-Latin equivalents (0123456789), or to strip HTML elements like <b\> from the stream.  An analyzer may have zero or more character filters, which are applied in order.

#### 自己理解

类似于java web中的filter，对请求来数据做过滤，字符过滤器可以有多个，接受原文并使用流的方式进行处理

## tokenizers

A *tokenizer* receives a stream of characters, breaks it up into individual *tokens* (usually individual words), and outputs a stream of *tokens*. For instance, a whitespace tokenizer breaks text into tokens whenever it sees any whitespace. It would convert the text `"Quick brown fox!"` into the terms `[Quick, brown, fox!]`.

The tokenizer is also responsible for recording the order or *position* of each term and the start and end *character offsets* of the original word which the term represents.

An analyzer must have exactly one tokenizer

#### 自己理解

分词器，将长文本打成令牌流，比如空白分词器按照空白分隔，默认的通常分成一个个词，分词器还负责记录每个术语的顺序或位置以及该术语所代表的原始单词的开始和结束字符偏移量。一个分析器至少需要一个令牌生成器

##  token filters

A *token filter* receives the token stream and may add, remove, or change tokens. For example, a lowercase、 token filter converts all tokens to lowercase, a stop token filter removes common words (stop words) like the from the token stream, and a synonym token filter introduces synonyms into the token stream.

Token filters are not allowed to change the position or character offsets of each token.

An analyzer may have zero or more token filters which are applied in order.

#### 自己理解

令牌过滤器，过滤分词器生成的令牌流，进行一些处理，比如小写 ，停止语，同义词处理。令牌过滤器不允许改变任意一个令牌的位置或者令牌的字符偏移量

## index and  search analysis

Text analysis occurs at two times: 

* *index time*
* *search time*

#### 自己理解

官方文档说的是文本分析会发生两次，第一次是index time，意思是保存文本的时候，分析器会把文本通过文本过滤器，分词器，令牌过滤器进行处理，生成一些term；第二次是search time ，意思是把用户的搜索词进行分词，比如用户搜索 Quick fox ，搜索分析器（可能与index分析器不一样）会把quick fox进行分词生成对应的term，通过这些term去与索引生成的term进行对比。

* 为什么一般使用同一种分析器： index跟search会生成同样的term，可以按照用户期望匹配文档
* 什么场景index跟search不一样： 比如使用默认的分析器，index将apple分解会生成[a ,ap, app, appl,apple],如果这时候用户查询appli,用户潜在查询的词可能appliance或者application，但是使用与默认一样的搜索分析器 appli分解成[a,ap,app,appl,appli]还是有很大的可能性会匹配到apple，这个时候选用与默认不一样的搜索分词器直接生成appli可以避免匹配到apple

## 中文分词与默认分词的差别

中文需要考虑语义环境，给它分开成一个个单字对于搜索来说是没有意义的