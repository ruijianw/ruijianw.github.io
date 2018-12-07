---
title: Tech talk notes-ML Infra @ Spotify
date: 2018-12-06 23:17:49
updated: 2018-12-06 23:17:49
tags:
mathjax: true
---

I attended the tech talk at spotify tonight, learned a lot of good stuff, many thanks to Spotify to origanize such great event and great representation from Ramino Yon.

Main takeaways, six challenges when doing machine learning infrastructure and some pointers to existed papers/blogs about ML infrastructures.

#Challenges
## 1. Rely on data standards
The data standards here means, there should be a universal data structure to hold machine learning data(e.g. categorical/numerical/ features). One good example is Tensorflow's [`tf.example`](https://www.tensorflow.org/tutorials/load_data/tf-records).

One big benefit from data standards is to allow data flow from components to components easily. In a machine learning system, after collecting the data, the first step usually is data preprocessing(Spotify has a open source implementation called [featran](https://github.com/spotify/featran)). After that we can use different machine learning algorithms. With data standards, we can easily drop a new component in the pipeline without many code changes.
## 2. Share logic & weights
The weights here means two parts:

$$\theta_{feature\ processing}$$ means the parameters ironed in feature transformation component, for example we need to record the average value of certain column to do the standarization $X' = \frac{X-\mu}{\delta}$
$\theta_{model}$ means the parameters learned in a machine learning model, like weights and bias in a logistic regression.

The major takeaway is, we should seal feature processing step and model prediction in the same container, so that we won't have wrong wires in components.

That actually triggered my memory regarding a production error we made, we accidentally merged a PR that replaced the tokenizer and we didn't record the tokenizer version in model, that causes a lot of complaints from our customer. After that accident, we started to add one more entry in the model config file, to remember the tokenizer version.

Since the presentation mainly talked about structured data, it is a little bit different from unstructured data such as text, NLP depends on other primitive NLP models to extract features(tokenization, entities, POS, syntax, etc.), that will lead you to a dependcy hell.

And one more difference I am thinking between ML infrasturce presented tonight and in our daily jobs. The data for Spotify's machine learning models usually are expected(recommending music, display ads), while what we are doing is Machine Learning as a Service, the challenge comes from the diversity of the data. The data can be from any domains and you have to use one single algorithm to fit them otherwise it would be hard to scale out your service.

## 3. Share decoration logic
This is inspired by Google's blog, see [Pointers](#Pointers) 1
After a model is online, we continue to collect more and more data. 

In the meanwhile, we need to get data from different data pool and join/compose them together before sending them to a machine learning system(include feature processing and model prediction). And we also want to retrain the model once we get more and more data, a good trick is log the decorated/joined data in runtime in a database, once we need to retrain the model, we don't need to redo the join/compose step again.

## 4. Validate your data
When buliding the machine learning model, we used to make some assumptions(e.g. age cannot be negative), this is easy to gurantee in training step, but we need to guard the input when serving the model, propably trigger some alerts when too many outliers are detected.

Some off-the-shelf implemention is Tensorflow's [`TFDV`](https://www.tensorflow.org/tfx/data_validation/get_started), which `compute the statistics`, `infer a schema`, `detect data anomalies` for given dataset.

By validating your newly collected data against historical/training data, you can also catch the data shifting.

## 5. Use "stateless" containers
It is hard to have completely "stateless" containers. By making it as "stateless" as possible, on-call engineers can easily revert the machine learning model to previous version once some bugs happened in current version. Another benefits is make the new model deployment easier.

Don't include any customized logic inside the container, it would significantly decrease the maintainability.

## 6. Leverage CI/CD for ML
CI/CD drastically change the way that software development. Other than the traditional benefits, CI/CD in machine leanring systems is more complex, some takeaways:
 a. Use it a lot, both offline and online
 b. Use low user impact environment
 
There is also an interesting way in Spotify to test machine learning model, they called it `Shadow`. Let's say we have model A in prodcution and model B is a newer version and ready to deploy.

After deploy model B and before route all traffic to B, mirror all traffic that to A, and send them to B. User will only receive predctions from model A, while the engineers should monitor the status of model B, it is more like a load testing in machine learning world.


#Pointers:
###1. [Hidden Technical Debt in Machine Learning Systems](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf)
###2. [Rules of Machine Learning: Best Practices for ML Engineering](https://developers.google.com/machine-learning/guides/rules-of-ml/)
###3. [What’s your ML test score? A rubric for ML production systems](https://storage.googleapis.com/pub-tools-public-publication-data/pdf/45742.pdf)
