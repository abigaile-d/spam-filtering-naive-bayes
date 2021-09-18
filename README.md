# Spam Filtering using Naïve Bayes Classifier

The Naïve Bayes algorithm can be used for document classification, such as filtering spam e-mails from legitimate messages. The Naïve Bayes algorithm works by constructing the vocabulary of words that appears in the training set and counting the number of spam and ham documents where each words made an appearance. By using these statistics into the Naïve Bayes formula, the probability of a new document to be a spam or ham can be computed. The document will belong to the class where the posterior probability is higher.

There may be cases when the word does not appear in a document class, and this will present a problem as the likelihood probability in this occasion will result to zero. However, this should not be the case as there is still a probability of that word occurring in a document belonging to that class in the future. To get around this problem, Lambda smoothing can be used on the class conditional likelihood.

Note: Please refer to external sources for the Naïve Bayes formula and discussion on this topic. I will only cover the program implementation in this article.

<h2> Program Summary </h2>

<b>Language used:</b> R

<b>Input file:</b>
- labels - contains the actual class and the filename (note: label file is provided in this repository)

<b>Output files:</b>
- vocabulary.txt – contains the word, spam count and ham count
- result.{lambda}.txt – document, actual classification, classification based on N.B.
- precision_recall_report.txt – precision and recall measures per lambda

<b>Settings:</b>

The program can be run with the below settings. Further instructions are specified in the jupyter notebook.

MENU 1: 
1. No Lambda Smoothing"
2. With Lambda Smoothing (run on all lambdas)
3. 200 Most Informative Words

MENU 2:
1. Construct new vocabulary
2. Load vocabulary from file

<h2> Dataset </h2>

The dataset used is the 2006 TREC Public Spam Corpora (https://plg.uwaterloo.ca/~gvcormac/treccorpus06/) which is a dataset for benchmarking spam algorithms. The dataset contains 37,822 e-mail documents pre-classified into spam or ham. 70% of this dataset was used for training the classifier while the other 30% was used as the test set.

The method used to partition the dataset is by applying the modulo of 10 to the document’s index, such that documents 1, 2, 3, 4, 5, 6, 7 are included in the training set, documents 8, 9, 10 are included in the test set, documents 11-17 in the training set, 18-20 in the test set, and so on.

Below is the distribution of spam and ham documents in the un-partitioned and partitioned datasets:
<table>
  <tr>
    <th></th>
    <th>HAM</th>
    <th>SPAM</th>
  </tr>
  <tr>
    <th>All dataset</th>
    <td>34.13357%</td>
    <td>65.86643%</td>
  </tr>
  <tr>
    <th>Training set</th>
    <td>33.98172%</td>
    <td>66.01828%</td>
  </tr>
  <tr>
    <th>Test set</th>
    <td>34.48793%</td>
    <td>65.51207%</td>
  </tr>
</table>

The partitioning is done this way so that more accurate comparison can be made on different test scenarios, however, using random sampling can also be done with comparative results.

<h2> Vocabulary Construction </h2>

To build the vocabulary, each document in the training set were parsed to get all the words satisfying the following condition: any sequence of alphabetic characters [a-zA-Z] delimited by a white space in front and a white space, comma, or period at the end. HTML tags are omitted. The words are converted to all lowercase characters. Duplicates, and words with less than 2 characters and greater than 50 characters were removed. These techniques were done to limit the vocabulary size which is a major factor in the execution time of the program. 

The words parsed and filtered are saved in a vector named words. Additional related vectors are vocabulary, wordctr_spam and wordctr_ham. All the words from the document being trained that are not yet in the vocabulary vector will be added to this vector. Then the wordctr vector corresponding to the words will be incremented, depending if the current training set document is labeled as spam or ham.

The vocabulary, the spam count, and the ham count will be saved in a file named “vocabulary.txt”. In the future when classifying a document, this file can just be read by the program to load the vocabulary and the word counts, so will not require re-training the classifier. <b>The vocabulary constructed using this implementation is composed of 76,184 words.</b>

<h2>Lambda Smoothing</h2>

There are cases during training the classifier wherein the word appeared in the spam class, but never appeared in the ham class, or vice versa. This will result to a probability of zero to the class where the word did not appear. However, there is actually a probability that the word will appear on this class in the future, which means that zero probability is not correct. This will also nullify the effect of the rest of the words to the overall probability because of the multiplicative nature of Naïve Bayes formula (0 times any number is 0). This is why lambda smoothing is added to the Naïve Bayes classifier.

The following values of λ were used in the program = 2.0, 1.0, 0.5, 0.1, 0.005. The precision and recall results for each lambda are reported below.

<h2>Top 200 Informative Words</h2>

Techniques from journal “Naive Bayes Spam Filtering Using Word-Position-Based Attributes” by Johan Hovold were also applied. The least frequent words and first 200 most frequent words were removed from the vocabulary. Then, since it was the goal to reduce the final vocabulary size to only 200 entries, the differences in the counter was used to determine the most informative words. The highest the gap between the spam and ham counts, the most probable the word is informative.

<h2>Results</h2>

<h4>Precision and Recall on Different λ Values</h4>

Below is the precision and recall using different lambda values, including 0.000, meaning no lambda smoothing included. The lowest recall is when there is no lambda smoothing. This is due to the cases when there are words in the vocabulary that did not appear in the class. This disrupts the Naïve Bayes formula. However, precision is still a bit high since those e-mails that can’t be classified was retained as ham (e.g. will not be moved from the user’s inbox).

The highest precision is attained when λ = 0.5 was used, while the highest recall is on λ = 2.0. Since it is more important for spam filtering to have a higher precision than recall, then the best lambda goes to λ = 0.5. Notice though that the lambda’s 0.5, 1.0 and 2.0 have close precisions and recalls.

<table>
  <tr>
    <th>LAMBDA</th>
    <th>0.000</th>
    <th>0.005</th>
    <th>0.100</th>
    <th>0.500</th>
    <th>1.000</th>
    <th>2.000</th>
  </tr>
  <tr>
    <th>PRECISION</th>
    <td>98.65576%</td>
    <td>96.03102%</td>
    <td>98.24419%</td>
    <th>99.37381%</th>
    <td>99.30754%</td>
    <td>99.14796%</td>
  </tr>
  <tr>
    <th>RECALL</th>
    <td>95.77560%</td>
    <td>98.30486%</td>
    <td>97.86089%</td>
    <td>98.21068%</td>
    <td>98.39903%</td>
    <th>98.62774%</th>
  </tr>
  </table>

![image](https://user-images.githubusercontent.com/90839613/133879402-a346cd12-8124-4236-94b4-2924d9973adb.png)

<h4>Precision and Recall on the top 200 Informative Words</h4>

The precision and recall when only the top 200 informative words were used is reported below (λ = 0.5). This is lower that the above results so 200 words might
not be a sufficient number of vocabulary to define if the document is spam or ham, or the 200 words are actually not informative enough. This is an area for improvement.

<table>
  <tr>
    <th>PRECISION</th>
  <td>88.86466%</td>
  </tr>
<tr>
  <th>RECALL</th>
  <td>93.19252%</td>
  </tr>
  </table>
