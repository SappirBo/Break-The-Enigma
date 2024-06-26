# Break-The-Enigma
## Overview
The Enigma Machine is a famous encryption device used by the Germans, mainly during World War II, to secure military communications. The machine resembles a typewriter and works by entering letters through a keyboard, which are then encoded based on the configuration of several coils and a circuit board before being displayed as another letter.

This configuration allowed for a huge number of possible settings, making the Enigma's encryption very difficult (and almost impossible) to decipher.

![image](https://github.com/SappirBo/Break-The-Enigma/assets/92790326/d550c188-40f4-4250-af8f-d54f5482dd4d)

## Enigma Machine
The Enigma machine consists of three main parts:
1. Three rotors, there are five possible rotors, choose three of them and choose for the aforementioned three the order in which they enter the machine and in addition the starting point of their work (each rotor has 26 starting points as the number of letters in English).
2. A reflector that actually connects one letter to another - the order can be set and changed but each letter must always be connected to another letter.
3. A plugboard where you can mix the letters as you wish, that is, it is not mandatory, but you can connect two letters and - so that if it enters the machine, it will actually insert the and vice versa.

![image](https://github.com/SappirBo/Break-The-Enigma/assets/92790326/2d9bd778-9777-4cdc-b1ac-93ca613c35b1)

Now we consider the number of possible combinations of starting configurations and options of the machine:
* For the rotors (there are five possible, of which you have to choose three in some order): 5 * 4 * 3 = 60.
* For the starting points of each rotor (each rotor starts its rotation from a certain point between 0-25): 26^3 = 17,576.
* For the plugboard, there is an option to connect a pair of letters - or not to connect at all, therefore: 26! / (6! * 10! * 2^10) = 150,738,274,237,937,250.

So in total we get: 158,962,555,217,826,360,000 configuration options for the machine (**more than 158 quintillion**).

## WW2 Solution
In World War II, they managed to solve the enigma in the following way - there is a single congenital defect inside the machine, since it is a closed-circuit permutations, it is not possible that when running an enigma the signal that entered as an input will also come out as an output, therefore, given a known sentence in advance - many options can be ruled out.

The allies took advantage of the aforementioned weakness and the fact that every German letter would end with the phrase (some say "Heil Hitler" and some say "weather report") and thus already managed to significantly reduce the number of possible configurations - according to you they built the [Bomb machine](https://en.wikipedia.org/wiki/Bombe) which examines a multitude of possible options from the remaining configurations and then was leaving dozens to hundreds of last possibilities - which would have been checked manually.

![image](https://github.com/SappirBo/Break-The-Enigma/assets/92790326/9f401873-f7e9-48ac-8681-ece1928bb808)

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
#### Introduction:
Adaboost is an ensemble learning algorithm (combining several individual models) used for classification and regression tasks. Its main purpose is to combine several weak classifiers, usually simple models, into one strong classifier or predictor. The goal is to improve predictive performance over a single weak classifier by iteratively focusing on misclassified cases in the training data.

Adaboost works through an iterative process. Initially, each training instance is assigned equal weight. A weak classifier, usually a decision tree with limited depth (stump), is trained on this weighted data. After training, Adaboost evaluates the performance of the weak classifier. It then assigns higher weights to the misclassified cases, prompting the next weak classifier to focus more on these difficult cases. This process is repeated for a predetermined number of iterations or until a perfect model is obtained. The final model combines the predictions of all weak learners, where the contribution of each weak learner is weighted according to his performance.

Adaboost with decision trees as weak classifiers is particularly effective for binary classification tasks. It works well with databases that contain a mix of numeric and categorical attributes. However, Adaboost can also be applied to regression tasks and can work with different types of data. Its adaptability and strong performance make it suitable for a wide range of machine learning applications, especially when dealing with complex relationships within the data.

#### In our implementation:
Since the algorithm combines several weak rules to create a strong rule, we can use any permutation of a given level (letter index - that is, each index will serve as a certain level) as a weak rule and combine them. This way the algorithm will hopefully be able to learn the configuration pattern of the Enigma machine.

There is one problem we can foresee - even in case this algorithm works perfectly, it still always blocks on the longest word in the dataset - because our rules are only set to the maximum size of the dataset.

We will take the information in our database and initialize all the words along the word to a fixed size - because AdaBoost requires a feature vector with a fixed length. We will divide the data into training and testing and train the model - the training of the model will be carried out X times where X is the length of the longest word in our training database -> we would like that in each iteration, AdaBoost will learn the form of the permutation of the same index (in each index 26 letters are mapped to 26 other letters ).
