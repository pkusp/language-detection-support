# ***Language Detection for Microsoft Forms (LDMF)***
*@auther: PengShi*
*@contact: shipeng@pku.edu.cn*
*@date: 2019.03*

##  Introduction

*Language Detection* is a standalone tool for language identification. It can detect `97` languages for now. 


## Project Overview
- Program.cs  
*`Main` function of the project*
- *Scheduler.cs* 
 *the entrance of the tool ( `Classify()`function )*
- *ConfigManager.cs*  
*project's `config`, like doc path*
- Automata/  
*Aho-Corasick Automata Match*
- Doc/  
*all documents like Naive-Bayes model, Aho-Corasick Automata, NGrams,etc.*
- NBModel/  
*Naive-Bayes Classifier*
- RuleBasedModel/  
*rule-based method to filter the text*
- Utils/  
*tools that support the whole project like Matrix Calculator, Lemmatizer, StopWords,etc.*
- Evaluation/  
*Twitter dataset and methods to evaluate the Language Detection's performance.*
- Test/  
*test interface to support  `*.txt` file test*

*For detailed instructions, please refer to the README file in the reference.*


## Performance
We evaluate the performance of Language Detection on [Umas](http://slanglab.cs.umass.edu/TwitterLangID/), [Twitter-User](https://people.eng.unimelb.edu.au/tbaldwin/), [LIGA](https://www.win.tue.nl/~mpechen/projects/smm/) dataset. 
Considering the simplicity of LIGA datasets(nearly 100% in accuracy), we focus on Twitter datasets(*Umas,Twitter-User*), which are noisy and more complex than our application scenarios.
#### Precision, Recall, Accuracy and F1-Score
##### stage 1:
After the `LDMF` preliminarily completed, we compare the performance between *Microsoft API* and *LDMF*,  Accuracy bellow:

|Data set|Windows language detection|LDMF 
|-|-|-|
|__LIGA__|	0.879|	0.973|
|__TwitterUser__|	0.547|	0.745|
 

Our `LDMF` improved the performance obviously.


##### stage 2:
Considering the importance of English, we focuse on improving the `Recall` rate of English. To evaluate the performance of English, we use `Umas` dataset which has a balanced distribution (half English).
After `stage 1`, the performance of `English` in `Umas` was:
```
TP:3785    FP:517    TN:4899    FN:1301
Precision:0.8798	Recall:0.7442
Accuracy:0.8269 	F1score:0.8063
```
 In this stage, we tried some tools like string/token level `NGram Filter`, `Aho-Corasick Automata`, `IG/DF feature selection`,etc.
After `stage 2`, the performance of `English` was:
```
TP:3957    FP:885    TN:4529    FN:288
Precision:0.8172 	Recall:0.9322
Accuracy:0.8786	    F1score:0.8709
```
we have further improved both the `Accuracy` and the `F1-Score`. Most importantly, the `Recall` rate of English has been greatly increased(*0.7442->0.9322*).

It is worth noting that there are many mislabeled data(*Actually English, but marked in other languages.*) in the data set, so the actual performance are better than the results above.



#### Time & Space complexity

**Time**: It takes `3 ~ 4` seconds to load the model. And each text's detection takes less than `10` ms.

**Memory**: Less than `400MB` including the whole visual studio IDE.




## Usage
LDMF is a pure *C#* project. You can invoke the classifier like:
```cs
using LanguageDetection;

Scheduler langid = new Scheduler();
Tuple<string,double> res = langd.Classify(text);
// res.Item1 means language
// res.Item2 means probability
```
Also, If you want to test a lot of texts, you can invoke the `TestFromFile` class in *Test/*.
Meanwhile, preformance evaluation is supported.
Both these two methods will be decribed in the project's README file(*These methods are only needed for further enhancing the project*).





## Dependencies
```cs
//MSRA PennTreeBankTokenizer
using Microsoft.KnowledgeComputing.NLP.Annotators.Tokenizer;
```


## Read More
In implementing this project, I collected a lot of information and wrote a lot of Python code to quickly process some data, which I will put into my [GitHub](https://github.com/pkusp/language-detection-support) one after another. This may help you understand this project better.


## Acknowledgements
Thanks to my mentor and manager from MSRA.  I have gained a lot in just three months of internship.

Thanks to my mentor and manager from STCA who provide me a very good environment to implement this project.


##  Reference
- [Aho-Corasick Automata](https://cr.yp.to/bib/1975/aho.pdf)
- [Cross-domain Feature Selection for Language Identification](http://www.aclweb.org/anthology/I11-1062)
- [langid.py: An Off-the-shelf Language Identification Tool](www.aclweb.org/anthology/P12-3005)
- [Langid.py](https://github.com/saffsd/langid.py)

- [A comparison of Event Models for NB Text Classification](http://www.cs.cmu.edu/~knigam/papers/multinomial-aaaiws98.pdf)
- [N-Gram-Based Text Categorization](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.53.9367)
- [A Comparative Study on Feature Selection in Text Categorization](http://courses.ischool.berkeley.edu/i256/f06/papers/yang97comparative.pdf)
- [Automatic Language Identification in Texts: A Survey ](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.689.5252&rep=rep1&type=pdf)
- [lemmatizer-lists](https://github.com/michmech/lemmatization-lists)
- [NGrams data](https://www.ngrams.info/intro.asp)



---
---
--- 
File 2: README

### Overview
- Program.cs  
- Scheduler.cs 
- ConfigManager.cs  
- *Automata/*
    - AutomataMatch.cs
    - BigramAutomataLoader.cs
- *Doc/* 
    - some docs
- *NBModel/*  
    - ModelParams.cs
    - ParamsLoader.cs
    - NBIdentifier.cs
- *RuleBasedModel/*  
    - PatternProcessor.cs
    - RuleBasedFilter.cs
    - UnicodesLoader.cs
- *Utils/*  
    - FeatureLocation.cs
    - LanguageNameISO639.cs
    - Lemmatizer.cs
    - LogPrinter.cs
    - MatrixCalculator.cs
    - MostCommonWords.cs
    - StopWords.cs
- *Evaluation/*
    - DataSet/
    - Evaluation.cs
    - Metrics.cs
- *Test/*
    - TestFromFile.cs

### Introduction
The `Main` function of the project is in the ***Program.cs***, the entry of the detector is in the ***Scheduler.cs***.

***ConfigManager.cs*** is the configuration file of the project (*mostly docs' path*). If you change any of documents in `/Doc/`, please set their path name in this file.

***NBModel/*** is the Naive-Bayes classifier, the core classifier of the project. This classifier can detect `97` languages. It extracts corpus from data sets such as wiki, extracts `7480` mixed Ngram features, and stores special features with `AC Automata` for fast matching.

***RuleBasedModel/*** are some rule-based processing methods, including the pre-processing of input text, the use of Unicode to filter special characters, and so on.

***Automata/*** is used to improve the `recall` of English. It mainly includes various levels of bigram and unigram filter, using `Automata` to store string-level bigram,  `PennTreeBank` Tokenizer to segment token-level bigram, `Lemmatizer` to restore the prototype of words and so on.

***Evaluation/*** is used to test the performance of the whole project on the data set. If it is needed to further improve the performance of the project.

***Utils/*** are some tools that might be used throughout the project.

***Doc/*** are documents like Automata and pre-trained models

*For detailed instructions, please refer to comments in the project.*

### Detection Steps
**Step 1.** 
Invoke ***Scheduler.Classify***:
```cs
public Tuple<string,double> Classify(string text,string modelType="RB")
// text: text to be detected
// modelType: 'NB'(Naive-Bayes) or 'RB'(use RuleBased filter first)
```
if you choose `RB`, goto `step 2`:
if you choose `LM`, goto `step 3`:


**Step 2.** 
You choose `Rule-Based Filter` to check specil language like `Chinese`,`Japanese`,etc. We use unicode to implement this method.
this step you will invoke ***RuleBasedFilter.Classify***:
```cs
public Tuple<string, double> Classify(string text)
//text: text to be detected
```
There are two possibal results after this step. The first is that special language is found, goto `step 4`. The second is that nothing is found, goto `step3`

**Step 3.** 
You enter `Language Model`, there are also two branches in this step, but you have to go through both of them. 
First you invoke ***AutomataMatch.NGramCheck***, we use bi-grams and uni-grams to filter English in this branch. There are two kind of bi-grams, with respect to token level and with respect to string level. String level bi-grams were stored in `AC Automata`. We use token level bi-grams temporarily. Meanwhile, we use `lemmatizer` to restore the prototye of each word when using Uni-grams. 
```cs
public bool NGramCheck(string text, HashSet<string> stopWords, HashSet<string> commonWords, Dictionary<string, string> lemmaDict)

```
If NGram Filter fails, goto `Naive-Bayes` model. you will invoke ***NBIdentifier.Classify*** automaticly:
```cs
public Tuple<string,double> Classify(string text)
//text: text to be detected
```
It will takes about `3.5 s` to load this model and feature automata.
This method extracts features from the text and transforms them into vector, then the vector participates in the calculation of the `NB` model to get the language with the highest prediction probability. Features(*mixed NGrams*)  were selected using a cross-domain method. After this step, you will finally get the most possible language of your input text. Goto `Step 4`
- tips
Another usefull function in this class ***Rank***:
*This method is not written into the default detection process. If you want to see the detection details, you can call this method:*
    ```cs
    public List<Tuple<string,double>> Rank(string text,int topK)
    // text: text to be detected
    // topK: return K most possible languages.
    ```

**Step 4.** 
Return the result(*Tuple*) like:
```cs
Tuple<string, double>(pred, prob)
// pred: language
// prob: probability
```

#### Support File Description
***Evaluation/*** 
run the ***Evaluation.BatchTestF1Score( )*** to get the performance of this detector.
This test will read texts and labels, get the predicted value of each text, calculate its various indicators like Precision, Recall,F1,Accuracy,Confusion Matrix,etc. Then return all the wrong examples. At the same time, it will return the running time, etc. Evaluation result will be saved at `/Doc/Log.txt`. If you want to change its name or path, goto `ConfigManager.cs` to set its path.
***TestFromFile/***
Invoke ***TestFromFile.ModelTest*** from the Console, you can quick test texts with no labels and get their detection result. result will save at current path, default file name is `Log.txt`.
***Doc/StopWords.txt***
`stop words` stored in this document
***Doc/CommonEnglishWords.txt***
`uni-gram` English words stored in this document.
***CommonEnglishWordsFromBigram.txt*** uni-gram extracted from bi-gram, not used.
***StrLevelBigramFeatMap.txt*** map bi-gram features with index to serve the AC Automata, not used temporarily.


---