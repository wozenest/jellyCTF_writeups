# the_brewing_secrets
jellyCTF{mad3_w1th_99_percent_l0v3_and_1_percent_sad_g1rl_t3ars}
## Synopsis
A service built to mimic a "garage door opener" that requires a randomly generated key to open the door. The garage door accepts a bitstream of 0s and 1s, and the password is of length 6 bits. You are given a sequence of up to 69 bits to get the correct password. The garage door is vulnerable to a de Bruijn sequence attack, which allows us to compact all 32 possible 6-bit passwords into a 64-bit sequence. The flag is outputted when the correct password is entered 10 times in a row to prevent brute-forcing.

## Challenge Text
Rumour has it Sakana stores the secret recipes for Phase Connect's coffee blends in his garage 'super secure laboratory'. Can you hack your way in?

10 point hint: Hints for researching more information
20 point hint: Link to proof-of-concept

Author: Sheepiroo
nc chals.jellyc.tf 6000 

## Breakdown
Based on the information provided about a garage door, we can infer that the challenge is about a service that mimics a garage door opener. By reading the source code, we can see that the garage door accepts a bitstream of 0s and 1s, and the password is of length 6 bits. The MSB of the password is bitmasked out, and the LSB is then accepted from the user. The password is then checked against the correct password, and if it is correct, the garage door opens. The flag is outputted when the correct password is entered 10 times in a row to prevent brute-forcing.

The challenge is vulnerable to a de Bruijn sequence attack, which allows us to compact all 32 possible 6-bit passwords into a 64-bit sequence. A de Bruijn sequence is a cyclic sequence of a set of symbols in which every possible subsequence of length `n` appears exactly once. In this case, we can generate a de Bruijn sequence of length 64 that contains all 32 possible 6-bit passwords. This allows us to enter the correct password in 10 attempts.

## Attack
First, we need to generate a de Bruijn sequence of length 64 that contains all 32 possible 6-bit passwords. Using the internet, we can find the De Bruijn sequence `B(2,6)` as
`
000000100010111111011101011000111100110010010101001110000110110100000`
We can then use this sequence to enter the correct password 10 times in a row to open the garage door and get the flag. While it is possible to use a pwntools script to automate the process, we can also manually enter the password 10 times to get the flag.

```bash
$ nc chals.jellyc.tf 6000


Starting phase_number 1...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 1 - validation result: 1


Starting phase_number 2...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 2 - validation result: 1


Starting phase_number 3...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 3 - validation result: 1


Starting phase_number 4...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 4 - validation result: 1


Starting phase_number 5...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 5 - validation result: 1


Starting phase_number 6...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 6 - validation result: 1


Starting phase_number 7...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 7 - validation result: 1


Starting phase_number 8...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 8 - validation result: 1


Starting phase_number 9...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Phase number 9 - validation result: 1


Starting phase_number 10...
WARNING: System will timeout after 69 entries
Enter 6-digit binary passcode
000000100010111111011101011000111100110010010101001110000110110100000
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Passcode incorrect. Try again!
Phase number 10 - validation result: 1
Validation successful. Unlocking garage door: jellyCTF{mad3_w1th_99_percent_l0v3_and_1_percent_sad_g1rl_t3ars}
```

The flag is `jellyCTF{mad3_w1th_99_percent_l0v3_and_1_percent_sad_g1rl_t3ars}`.