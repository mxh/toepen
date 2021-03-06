# DeepToep

This repository contains the code of DeepToep, a Deep Reinforcement Learning AI for a
heads-up only variant of the Dutch card game
[Toepen](https://en.wikipedia.org/wiki/Toepen). An explanation of the game can be found
below.

## DQN setup
The AI is driven by a [Deep Q-Network setup](https://deepmind.com/research/dqn/), adapted
with [Double Q-Learning](https://arxiv.org/abs/1509.06461) and [Dueling DQN](https://arxiv.org/abs/1511.06581).
It uses Boltzmann exploration as action-selection strategy. It is entirely trained from self-play.

### State representation
TODO

## Performance
I tested DeepToep, trained for 50000 episodes, against a number of heuristic
strategies. Each pair is tested on 1000 random full games (from 0 to 15 points,
see game play), switching the starting order every time (resulting in a 50/50
split). DeepToep significantly out-performs each of them, as can be seen in the
win rate table below. Of course, better heuristics are conceivable, and I might
add these later.

The strategies tested against are:

| Heuristic         | Description                                                                                                |
| ----------------- | ---------------------------------------------------------------------------------------------------------- |
| Random            | Fully random play                                                                                          |
| Random, no fold   | Never fold, otherwise same as Random                                                                       |
| LowestCard        | Always play lowest possible card. Betting is random.                                                       |
| LowestCardNoFold  | Never fold, otherwise same as LowestCard                                                                   |
| HighestCard       | When following suit, play highest available card. Otherwise play lowest available card. Betting is random. |
| HighestCardNoFold | Never fold, otherwise same as HighestCard                                                                  |

The win-rates:

| Winner \ Loser    | DeepToep          | Random            | RandomNoFold      | LowestCard        | LowestCardNoFold  | HighestCard       | HighestCardNoFold |
| ----------------- |------------------ | ----------------- | ----------------- | ----------------- | ----------------- | ----------------- | ----------------- |
| DeepToep          | X                 | **93.50**         | **81.80**         | **89.00**         | **79.10**         | **85.60**         | **78.50**         |
| Random            | 6.50              | X                 | 14.00             | 52.20             | 18.10             | 54.70             | 18.20             |
| RandomNoFold      | 18.20             | 86.00             | X                 | 76.80             | 40.50             | 75.30             | 39.60             |
| LowestCard        | 11.00             | 47.80             | 23.20             | X                 | 55.50             | 48.30             | 51.50             |
| LowestCardNoFold  | 20.90             | 81.90             | 59.50             | 44.50             | X                 | 51.30             | 51.10             |
| HighestCard       | 14.40             | 45.30             | 24.70             | 51.70             | 48.70             | X                 | 47.40             |
| HighestCardNoFold | 21.50             | 81.80             | 60.40             | 48.50             | 48.90             | 52.60             | X                 |

## The game of Toepen
Toepen is played with a 32-card deck, called a *piquet deck*. It is a subset of
a regular 52-card deck, but with the 2s - 6s removed. The value ordering of the
cards is different from most games. From low to high, the cards are ordered Jack,
Queen, King, Ace, 7, 8, 9, 10.

![The 32-card piquet deck used in Toepen](https://github.com/mxh/toepen/raw/master/img/piquet_deck.png)

Toepen is a [trick-taking game](https://en.wikipedia.org/wiki/Trick-taking_game),
with the interesting twist that only the *last* trick matters - whoever wins
the last trick, wins the round.  After each round, penalty points are awarded
to everyone but the winner, based on the current *stake* of the game, which can
be increased during play.

## Gameplay of Toepen
At the start of a round, the stake of the game is set to 1, and one player is
selected to be the dealer, who deals 4 cards to each player. The player next to
the dealer (the *leading player*) starts the game, and leads with a card from
her hand by playing it face up in front of her. Play then continues to the next
player, who has to follow suit, i.e. has to play a card of the leading suit if
she can. This continues until each player has played a card, after which the
player with the highest card wins the trick, and becomes the leading player for
the next trick.

## Winning the round
The player to win the *last trick* wins the round. All the other players get a
number of penalty points equal to the current stake.

## "Toeping"
With just the above rules, the game would be quite a boring affair. To spice
things up, a player can opt to raise the stakes of the game during their turn by
knocking on the table, in an act called "toeping". When a player toeps, he is
raising the stake of the game by 1 point. The next player then has to say whether
he will call, fold, or toep (again raising by 1 point). This continues until all players
except for the last player to toep have either called or folded. Play then continues
as normal, with the player who started the toeping in the first place.

*A player can only toep if he was not the last person to have toeped.*

## Example round
An example game will help make the game clearer. Players Andrea and Bart are playing
a round.

Andrea is dealt a fairly good hand: J&diams;, 8&spades;, 8&diams;, 10&hearts;,
with the J&diams; being her main weakness.

Bart has been dealt a less than ideal hand: Q&diams;, Q&spades; K&spades; 9&hearts;.
Remember, face cards are weak in Toepen, so knowing both starting hands we see that
Andrea has quite the advantage.

![The starting hands of our players Andrea and Bart](https://github.com/mxh/toepen/raw/master/img/toep_example_game_start.png)

### Trick 1
Andrea leads the first trick and plays the J&diams;, getting rid of her main
weakness. Bart has to follow suit and thus responds with the only diamonds card
in his hand, the Q&diams;. This beats Andrea's Jack, and thus in trick 2, Bart
will be the leading player.

![After trick 1, Bart is the leading player](https://github.com/mxh/toepen/raw/master/img/toep_example_game_trick_1.png)

### Trick 2
Bart does not have too many good options here, but decides most importantly to hold on to his 9&hearts; as his only
strong card. He plays the Q&spades;.

Andrea now knows she could just beat this card by playing the 8&spades;, but she is now
pretty confident that she could win the last trick, and thus decides to raise the stakes of the game
by toeping.

![Andrea decides to raise the stakes](https://github.com/mxh/toepen/raw/master/img/toep_example_game_trick_2_toep.png)

Bart knows his hand isn't fantastic, and knows he'll likely lose this trick as well as the next one, but he 
is hoping for a low hearts card on the last trick, and decides to call (I'll leave it up to you to decide whether this is foolish or not).

Andrea finishes the trick by playing the 8&spades;.

### Trick 3
Andrea decides to lead with the 8&diams;, keeping her best card for the last round. She would like to toep again, but
this is not allowed - the last player to have raised the stakes cannot raise the stakes again. However, Bart decides to try
and trick Andrea by bluffing - he decides to toep, feigning a high diamond card, in hopes of Andrea folding. No such luck - Andrea
does not believe Bart, and not only calls his bluff but raises him, taking the stake to 4. Bart does not reinforce his bluff
and plays the K&spades; with his tail between his legs.

![Bart's bluff fails miserably](https://github.com/mxh/toepen/raw/master/img/toep_example_game_trick_3.png)

### Final trick
Finally, Andrea is left just with one move, which is a sure victory - she plays the 10&hearts;, the highest card in its suit.
Bart perfunctorily responds with the 9&hearts;, and loses the game with a penalty of 4 points.

![Andrea wins the round](https://github.com/mxh/toepen/raw/master/img/toep_example_game_trick_4.png)

