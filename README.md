# Break-The-Enigma
## Overview
The Enigma Machine is a famous encryption device used by the Germans, mainly during World War II, to secure military communications. The machine resembles a typewriter and works by entering letters through a keyboard, which are then encoded based on the configuration of several coils and a circuit board before being displayed as another letter.

This configuration allowed for a huge number of possible settings, making the Enigma's encryption very difficult (and almost impossible) to decipher.

## Our Solution
First we wanted to try to solve the enigma with the help of modern tools, to see how it is possible to solve this cipher with the help of machine learning algorithms (ie given some encrypted sentence - find the original sentence). The problem here is fundamental: every time you press a letter - the configuration of the machine changes - therefore for a text of length x characters - the machine has to learn x possible configurations from all the options presented above.

## Data Set
We built the data set ourselves: after the reserve, to "get back in shape" for writing code - I wrote an implementation project of the legendary machine in Cpp, while preserving all its components and all the huge number of configurations it has. I wrote the machine carefully, taking into account all the small nuances that built it.

After that, I added to the machine an option of reading from text (according to the path of a file) and running the machine on the entire content of the text - so if the machine starts in state 0 of any configuration, for each letter in the i-th position in the text, the machine will encode the letter when it is in state i of Configuration 0 (ie in the initial configuration (state 0) run i transformations of the machine's configuration - this is the i-state of the machine).

I transferred the above method for writing the results into a text file where in each line of the file the first word is the original word (w) and the second word is the coded word (Enigma(w)) with a space between them.

In the beginning the dataset was built on top of the Wikipedia entries of the enigma - it amounted to a total of 16K words.

We tried several runs on several different models - but at most we reached a performance of 70% success - after consultation, we introduced Tiny Shakespeare - a collection of all Shakespeare's plays, the above collection contains more than 40K lines from Shakespeare's plays - something which increased the size of the dataset to more than 200K words in total.

## The Algorithms used
### AdaBoost (Adaptive Boosting) using Decision Tree
introduction:
Adaboost is an ensemble learning algorithm (combining several individual models) used for classification and regression tasks. Its main purpose is to combine several weak classifiers, usually simple models, into one strong classifier or predictor. The goal is to improve predictive performance over a single weak classifier by iteratively focusing on misclassified cases in the training data.

Adaboost works through an iterative process. Initially, each training instance is assigned equal weight. A weak classifier, usually a decision tree with limited depth (stump), is trained on this weighted data. After training, Adaboost evaluates the performance of the weak classifier. It then assigns higher weights to the misclassified cases, prompting the next weak classifier to focus more on these difficult cases. This process is repeated for a predetermined number of iterations or until a perfect model is obtained. The final model combines the predictions of all weak learners, where the contribution of each weak learner is weighted according to his performance.

Adaboost with decision trees as weak classifiers is particularly effective for binary classification tasks. It works well with databases that contain a mix of numeric and categorical attributes. However, Adaboost can also be applied to regression tasks and can work with different types of data. Its adaptability and strong performance make it suitable for a wide range of machine learning applications, especially when dealing with complex relationships within the data.

In our implementation:
Since the algorithm combines several weak rules to create a strong rule, we can use any permutation of a given level (letter index - that is, each index will serve as a certain level) as a weak rule and combine them. This way the algorithm will hopefully be able to learn the configuration pattern of the Enigma machine.

There is one problem we can foresee - even in case this algorithm works perfectly, it still always blocks on the longest word in the dataset - because our rules are only set to the maximum size of the dataset.

We will take the information in our database and initialize all the words along the word to a fixed size - because AdaBoost requires a feature vector with a fixed length. We will divide the data into training and testing and train the model - the training of the model will be carried out X times where X is the length of the longest word in our training database -> we would like that in each iteration, AdaBoost will learn the form of the permutation of the same index (in each index 26 letters are mapped to 26 other letters ).
