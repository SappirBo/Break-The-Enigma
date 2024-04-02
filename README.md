# Break-The-Enigma
## Overview
The Enigma Machine is a famous encryption device used by the Germans, mainly during World War II, to secure military communications. The machine resembles a typewriter and works by entering letters through a keyboard, which are then encoded based on the configuration of several coils and a circuit board before being displayed as another letter.

This configuration allowed for a huge number of possible settings, making the Enigma's encryption very difficult (and almost impossible) to decipher.

## Our Solution
First we wanted to try to solve the enigma with the help of modern tools, to see how it is possible to solve this cipher with the help of machine learning algorithms (ie given some encrypted sentence - find the original sentence). The problem here is fundamental: every time you press a letter - the configuration of the machine changes - therefore for a text of length x characters - the machine has to learn x possible configurations from all the options presented above.
