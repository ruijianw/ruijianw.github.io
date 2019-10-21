---
title: Handling out of vocabulary(OOV) word
mathjax: false
date: 2019-10-20 19:55:43
updated: 2019-10-20 19:55:43
tags:
---

Out of vocabulary problem is very common problem in NLP, it can be seen in text classification, named entity recognition, information retrieval, etc.


It is caused by multiple reasons and there are multiple solutions for it.


<!--more-->
# Reasons for OOV
## typos
Typos is great source to generate OOV word, especially on mobile phones without spell correction. You can easily type `hello` as `helo`
## different vocabularies
If your model is trained with a limited dataset, it is very likely you have a limited vocabulary. When you apply your model to new data, your model will fail to recognize some words. As for the reason to have a limited trainign data, there are many reasons, e.g.
 * new words are being created every second 
 * you cannot afford to annotate too many data

----

# Solutions
## Ignore
Ignoring is probably the worst solution of all, that means the model pretends to not see the word.

## UNK word
This solution is to reserve a dimension in feature space, it may eliminate some impact of OOV word, but very limited. Especially the OOV word plays an important role in the NLP task, e.g. some positive and negative words are OOV words in sentiment analysis.

## Feature Hashing
Feature hashing is not really designed for the OOV words, The idea is to blindly trust the hash collision to bring some good luck to the model. Of course it can go south a lot.

## Spell check
If you have observed a lot of OOV words are caused by typos(e.g. chatbot), it is pretty natural to use spell check, but one caveat is that, the spell check may over-correct some correct words, you have to use the corrected words as additional feature and combine this with other solutions together.

## Subword
I learned it from Facebook's `fasttext` library, it breaks down the word/string to multiple consecutive characters. For example, if you want to break `hello world` into 3-gram-char pieces within the word, you will have features like `hel`, `ell`, `llo`, `wor`, `orl`, `rld`. If you want to break the string into pieces, aka across the words, some additional features apart from the previous ones are `lo⎵`, `o⎵w`, `⎵wo` (`⎵` means white space).

`scikit-learn` already has it as a built-in feature, check the `Vectoriziers` under `sklearn.feature_extraction.text`, the `analyzer` parameter has a `char` and `char_wb` options, it corresponds to `subword across words` and `subword within word` accordingly.

In fact, it is also used in `MinHash` and `SimHash` for near-duplicate detection. Ask google for more details.


## Wordpiece/BPE/ULM
Those methods work similiarly like the `Subword` method, but more intelligently. Long word in short:
 * Wordpiece predefines a likelihood threshold or vocabulary size, 
   * initialize with characters in the training data
   * train a lanuage model
   * combine the word units that increase the likelihood of training data the most
   * until it hits the predefined conditions.
 * BPE(Bypte Pair Encoding) predefines a vocabulary size, and merge the most frequent word pieces(start from letters in English) until reach the desired vocabulary size
 * ULM(Unigram Language Model) also leverage the language model to segement words into subwords, but gives multiple subword segmentations with probabilites. 

## External knowledge
External knoledge can be as simple as synonym words, or as complex as knowledge graph, the purpose is to tech the model with prior knowledge apart from the training data. I have seen that being employed in low resource scenarios.


----
#Reference
1. [Schuster, Mike, and Kaisuke Nakajima. "Japanese and korean voice search." 2012 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP). IEEE, 2012.](https://ieeexplore.ieee.org/abstract/document/6289079)
2. [Sennrich, Rico, Barry Haddow, and Alexandra Birch. "Neural machine translation of rare words with subword units." arXiv preprint arXiv:1508.07909 (2015).](https://arxiv.org/abs/1508.07909)
3. [Kudo, Taku. "Subword regularization: Improving neural network translation models with multiple subword candidates." arXiv preprint arXiv:1804.10959 (2018).](https://arxiv.org/abs/1804.10959)

<!--
TODO:
1. Viterbi algorithm
2. Language model
3. why fixed vocabulary size in Neural Machine translation
-->