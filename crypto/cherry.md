# cherry
`jellyCTF{you_won_cherries!}`
## Synopsis
A web-based slot machine game that shows a 9 character message. The goal is to get triple cherries to reveal 9 characters of the flag. There are 3 settings in total, each revealing 9 characters of the flag. The flag is the concatenation of the 3 settings. The challenge is to find a way to get triple cherries in each setting to reveal the flag. We use SageMath to calculate the number of spins required to get triple cherries in each setting.
## Challenge Text
A Starknight hacked an old slot machine and turned it into something strange?! I heard that you win a secret message if you manage to get triple cherries, but...

Author: Meow Mix
https://cherry.jellyc.tf/ 
## Breakdown
We shall first connect to the website, using Firefox:
![cherry1](/assets/cherry_1.png)

Clicking on the "Spin" button, we see that the slot machine spins and stops at a "random" position:
![cherry2](/assets/cherry_2.png)

Playing a different number of coins, we see that it changes a different slot on the plain text message. Further, each slot has 3 plaintext characters, and it seems to be a "base-x" message.

There appear to be 2 buttons on the left side of the machine, Change and Reset. Clicking on Change seems to scramble the slot machine text, but clicking it 3 times seems to reset the text to the original message. Clicking on Reset seems to reset the slot machine to the original message. This likely means that the slot machine has 3 settings, each revealing 9 characters of the flag. The flag is the concatenation of the 3 settings.

Now, let's inspect the source code of the website. We see that there is JavaScript code that handles the spinning of the slot machine. 

[View source code](/assets/cherry_script.js)

Now, we see some crucial information in the source code:
```javascript
function reset() {
  if (ciphertextIndex == 0)       slotIndices = [10992, 30978, 12520];
  else if (ciphertextIndex == 1)  slotIndices = [30983,  7390,   481];
  else if (ciphertextIndex == 2)  slotIndices = [25974, 26744,  9122];
  spinCounts = [0,0,0];
  updatePlaintextDisplay();
  doSpinCleanup();
}
```
The beginning of the script also reveals a call
```
fetch('static/slotsymbols.txt')
```
This implies that the slot machine is using a list of symbols to display on the slot machine. We can download the list of symbols from the website and inspect it. 

[View list of symbols](/assets/slotsymbols.txt)

The important thing to note is that the cherry symbol is represented by the number 0. This means that we need to get `slotIndices === [0,0,0]` in each setting to reveal the flag.

```javascript
function playCoin(n){
  spinMode = n;
  if (n == 0)       slotSpins = [ 19,  22,  19];
  else if (n == 1)  slotSpins = [ 32,  27,  29];
  else if (n == 2)  slotSpins = [347, 349, 353];
  updateModeDisplay();
}
```

Now, we see that spinning the slot machine with 0 coins advances the slot machine by 19, 22, and 19 slots. Spinning the slot machine with 1 coin advances the slot machine by 32, 27, and 29 slots. Spinning the slot machine with 2 coins advances the slot machine by 347, 349, and 353 slots.

Lastly, we see that the slot machine's symbol display has a modulo operation applied to it:
```javascript
    slotIndices[columnIndex] = (slotIndices[columnIndex] + slotSpins[columnIndex]) % m;
```
Using the console, we get that `m=32768`. This will be important for our calculations.

Without further ado, we can begin our attack.

## Attack
We can use SageMath to solve this challenge. Using the information from the source code, we can calculate the number of spins required to get triple cherries in each setting. Using SageMath's built-in Matrix Solver over a modulo ring, we can calculate the number of spins required to get triple cherries in each setting.

```python
R = IntegerModRing(32768)
A=matrix(R,[[ 19,  22,  19],[ 32,  27,  29],[347, 349, 353]]).transpose()
B=matrix(R,[-10992, -30978, -12520]).transpose()
print("Setting 1")
print(A.solve_right(B))
B=matrix(R,[-30983,  -7390,   -481]).transpose()
print("Setting 2")
print(A.solve_right(B))
B=matrix(R,[-25974, -26744,  -9122]).transpose()
print("Setting 3")
print(A.solve_right(B))
```
Running the script, we get the number of spins required to get triple cherries in each setting:
```
Setting 1
[ 4194 29860 25598]
Setting 2
[10469  7226 14158]
Setting 3
[20582  3380 26344]
```
I transposed it to make it easier to enter into the JS console.

Finally, while it would be possible to implement my own "awascii32" converter, it was easier to use the one provided by the challenge author. I used the following script to get the flag:
```javascript
spinCounts = [4194, 29860, 25598];
updatePlaintextDisplay();
```
Giving this image:

![cherry3](/assets/cherry_3.png)

Repeating the process for the other 2 settings, we get the flag:
```
jellyCTF{you_won_cherries!}
```

Here is the script for the other 2 settings in case you wish to just copy and paste it into the console:
```javascript
spinCounts = [10469, 7226, 14158];
updatePlaintextDisplay();
```
```javascript
spinCounts = [20582, 3380, 26344];
updatePlaintextDisplay();
```
