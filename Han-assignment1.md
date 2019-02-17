### Question 1

#### Pair

This solution is implemented in `PairsPMI.java`. 

- Two jobs are included in this solution: one for the word count, which is taken from A0 and modified slightly, and one for the pair count. 
- Each job has one mapper, one combiner and one reducer.
- The first job (word count) writes the result as `SequenceFile` to `job1Output/` and the number of reducers is forced to be 1 so there is only one single output. I added a word `"***LINE_COUNT_MARKER***"` at the end of `map` function so the occurrence of this “word” count the number of lines. 
- The second job (pair count) reads the file and use it to construct a word map so we have the information of occurrence as well as the number of lines. 
- The mapper of second job simple emits each unique pair it sees, and the reducer calculates the PMI according to pair count, number of lines and word count accordingly.
- Final output is a unique co-occurence (wordA, wordB) string pair and a (PMI, times of co-occurence) string pair.

#### Stripe

This solution is implemented in `StripesPMI.java`. 

- Two jobs are included in this solution: one is exactly the same as `PairPMI.java` which is a simple word count job producing intermediate results to a `SequenceFile` in `job1Output/`. 
- Each job has one mapper, one combiner and one reducer. For the second one there is an additional Partitioner (To make sure the all stripes of a word goes to the same reducer).
- Again, the second job reads the word count result to obtain the number of lines, and construct word occurrence times map. 
- The mapper of second job emits a `Text` object as key, and a map `HMapStIW` as value (the stripe). 
- Finally, the reducer of the second job sums up the stripes for a word and read co-occurence from the stripe value, which allows it to calculate PMI.

- Final output is a unique co-occurence (wordA, wordB) string pair and a (PMI, times of co-occurence) string pair.

### Question 2

With combiners, the running time on `linux.student.cs.uwaterloo.ca` is as follows:

- Pairs: 4.057s (job of word count) + 24.519s (job of pairs count) = 28.576s (in total)
- Stripes: 3.961s (job of word count) + 8.417s (job of stripes count) = 12.378s (in total)

### Question 3

Without combiners, the running time on `linux.student.cs.uwaterloo.ca` is as follows:

- Pairs: 4.947s (job of word count) + 27.588s (job of pairs count) = 32.535s (in total)
- Stripes: 5.041s (job of word count) + 9.458s (job of stripes count) = 14.499s (in total)

### Question 4

```
   77198  308792 3002120
```

### Question 5

The answer for **Question 5 (Highest PMI)** is:

```
(maine, anjou)	(3.6331423021951013, 12)
(anjou, maine)	(3.6331423021951013, 12)
```

The answer for **Question 5 (Lowest PMI)** is:

```
(thy, you)	(-1.5303967668481644, 11)
(you, thy)	(-1.5303967668481644, 11)
```

### Question 6

The answer for **Question 6 (tears)** is:

```
(tears, shed)	(2.111790076876236, 15)
(tears, salt)	(2.0528122169168985, 11)
(tears, eyes)	(1.1651669643071034, 23)
```

The answer for **Question 6 (death)** is:

```
(death, father's)	(1.1202520304197314, 21)
(death, die)	(0.7541593889996885, 18)
(death, life)	(0.7381345918721788, 31)
```

### Question 7

```
(hockey, defenceman)	(2.412477838402177, 154)
(hockey, winger)	(2.366872758610535, 189)
(hockey, sledge)	(2.3323145806712415, 93)
(hockey, goaltender)	(2.2549042556644685, 203)
(hockey, nhl)	(1.9956310244851678, 988)
```

### Question 8

```
(data, cooling)	(2.097136695232473, 74)
(data, encryption)	(2.0436049601323107, 53)
(data, array)	(1.990156815381723, 50)
(data, storage)	(1.987071228818446, 110)
(data, database)	(1.8915470174957723, 100)
```





