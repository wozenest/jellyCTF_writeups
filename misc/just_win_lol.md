# just_win_lol
`jellyCTF{its_v3ry_stra1ghtf0rw4rd_s1mply_g3t_g00d_rng}`
## Synopsis
A challenge that requires you to win a probabilistic game of "Balatro" where you are required to get 5 of the same cards within a "hand" of 12 cards. The cards are assigned A-K, with all cards equally likely to be drawn. The challenge requires you to win the game 5 times within 10 attempts to get the flag. The vulnerability lies in the fact that the RNG is seeded with time, thus allowing us to predict the cards that will be drawn.
## Challenge Text
looks like that new Balatro game has already got some knockoffs. the RNG for this one sucks though - how are you ever meant to win?! reminder: do not bruteforce, you won't win

10 point hint: pointer on where to start looking

20 point hint: advice on what to start doing

Author: arepi

https://just-win-lol.jellyc.tf/

## Breakdown
Connecting to the website, we see that we are presented with a game of "Balatro", however, the UI for the game is mostly broken, except for the "Draw" and "Reset" buttons, the "Reset" button resets the score and gets us back to 10 hands remaining, while the "Draw" button draws a hand of 12 cards. The goal of the game is to get 5 of the same cards within a hand of 12 cards. Looking at the UI on the website
![just_win_lol_1](/assets/just_win_lol_1.png)
It is clear that we must get 5 of a kind to score points. Using some trial and error, we see that every time this happens, we are given 1000 points, and we need to get 5000 points to win the game. The game is also limited to 10 hands, and we need to win the game 5 times within 10 attempts to get the flag.

Looking at the source code of the website, we see that it is very primitive, and we see that the attack surface is minimal, and likely the vulnerability lies in the RNG, based on the challenge text. Looking at the [source code](/assets/just_win_lol.go), we observe
```go

func randHand(r rand.Rand) []card {
	hand := make([]card, handSize)
	sum := 0
	for sum < handSize {
		hand[sum] = card{
			value: cardValues[r.Intn(len(cardValues))],
			suit:  cardSuits[r.Intn(len(cardSuits))],
		}
		sum++
	}
	return hand
}
...
func main() {
    ...
    mux.HandleFunc("/hand", func(w http.ResponseWriter, r *http.Request) {
        // current time in unix seconds
		var timeNow = time.Now().UTC().Unix()
		var rand_time = rand.New(rand.NewSource(timeNow))
		hand := randHand(*rand_time)
        ...
        if isFiveOfAKind(hand) && sessionManager.GetInt64(r.Context(), "lastWin") < timeNow {
			sessionManager.Put(r.Context(), "lastWin", timeNow)
			currentWins += 1
		}
        ...
    }
}
```
We see that the RNG is seeded with the current time in Unix seconds (luckily!, milliseconds would have been brutal as server latency would be significant). This means that we can predict the cards that will be drawn, and thus win the game. We can write a script to predict the cards that will be drawn and print the time that the cards will be drawn to win the game. We then manually play the game 5 times to get the flag. While it is possible to automate the process, we can also manually play the game 5 times to get the flag, as automation may take longer than manually playing the game.

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

type card struct {
	value string
	suit  string
}

func (c *card) String() string {
	return fmt.Sprintf("%s%s", c.value, c.suit)
}

var cardValues = []string{"2", "3", "4", "5", "6", "7", "8", "9", "T", "J", "Q", "K", "A"}
var cardSuits = []string{"h", "c", "d", "s"}
var handSize = 12
var requiredWins = 5

func randHand(r rand.Rand) []card {
	hand := make([]card, handSize)
	sum := 0
	for sum < handSize {
		hand[sum] = card{
			value: cardValues[r.Intn(len(cardValues))],
			suit:  cardSuits[r.Intn(len(cardSuits))],
		}
		sum++
	}
	return hand
}

func isFiveOfAKind(hand []card) bool {
	counts := make(map[string]int)
	for _, card := range hand {
		element := card.value
		counts[element] = counts[element] + 1
		if counts[element] >= 5 {
			return true
		}
	}
	return false
}

func main() {
	var timeNow = time.Now()
	for true {
		var timeNowTS = timeNow.UTC().Unix()
		var rand_time = rand.New(rand.NewSource(timeNowTS))
		hand := randHand(*rand_time)
		if isFiveOfAKind(hand) {
			fmt.Println("Five of a kind found!")
			fmt.Println(timeNow)
		}
		timeNow = timeNow.Add(time.Second)
	}
}
```

Running the script, we get the times that the cards will be drawn, and we can manually play the game 5 times to get the flag.

Sure enough, after playing the game 5 times, we get the flag:
![just_win_lol_flag](/assets/just_win_lol_flag.png)

```
jellyCTF{its_v3ry_stra1ghtf0rw4rd_s1mply_g3t_g00d_rng}
```
